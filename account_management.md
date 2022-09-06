# Account Management

The methods here are used to manage several ExorLive accounts.

![OrganizationAccessForApis](/images/ApiOrganizationsAccess.png)


You, as a partner of ExorLive and owner of the "partner application" has one ExorLive account for your own company. It is used to get your application the first access to ExorLive. You have many customers who are users of your application. Each of the customers may have one ExorLive account as well. We call one account for one "ExorLive Organization". Each account may have one or more users.

**Pre-authorized access**: You will get an OAuth 2.0 Application Key from us and implement access to our API according to [the authentication description here](/authentication.md).

**Customer approved access**: Is described below.

**Test and Production**: During your development phase you probably want to have a test environment. Therefore, we recommend to duplicate this setup for two sets of accounts. One for test and one for production. Hence, we will set up two Partner accounts in ExorLive and issue you two sets of Application Keys.

## Customer approved access
Each ExorLive Organization must be linked to your application so that your application may access the ExorLive data of that account. The methods described here are used to manage that access.


Your customer must approve that you access their ExorLive account. They do that by typing their ExorLive username and password in a form that you provide.

Example ExorLive login form:

![ExorLiveLoginForm](/images/PartnerConnectForm.png)

Here is a link to an ExorLive logo you may use:
[http://developer.exorlive.com/images/exorlivelogo.png](http://developer.exorlive.com/images/exorlivelogo.png)

This form is used only once for each customer (each ExorLive Organization). ExorLive will store that the connection is approved for that organization.


Here are the steps you need to implement:

1. Check your own records. If this customer has already done this connection to ExorLive, you are done.

2. Show a form similar to the one above.

3. Call [PartnerChallenge](#partnerchallenge) to get a "salt" to encrypt the password.

4. Run the password encryption algorithm [(C# and Javascript code provided)](#encryption-algorithm).

5. Call [PartnerLinkOrganizationToApplication](#partnerlinkorganizationtoapplication).

6. If the call fails, show appropriate error message.

7. If the call is success, store the ExorLive User Id of that ExorLive user the customer provided and use that User Id to get an accesstoken for that user using the [authentication](/authentication.md) methods.

## Encryption algorithm

First run the SHA1 hash algorithm on the password. Result is a lowercase hex string. Then run the HMAC1 hash algorithm on the result with the challenge as salt/key. Result is an uppercase hex string.

Here is the javascript code to accomplish this:

```js
Include: https://exorlive.com/resources/Scripts/SHA1.js
....							
var sha1 = new SHA1();
var shaMACPwd = sha1.hex_hmac_sha1(sha1.hex_sha1(txtPassword.value), challenge);
....
```

Below is some C# code to accomplish the same

```c#
public static string EncryptPassword(string password, string challenge)
{
	SHA1 shaEngine = SHA1.Create();
	Byte[] b1 = Encoding.UTF8.GetBytes(password);
	Byte[] b2 = shaEngine.ComputeHash(b1);
	string shaPwd = ByteToHex(b2).ToLower();
	string shaMacPwd = HexHMACSha1(shaPwd, challenge);
	return shaMacPwd;
}

private static string HexHMACSha1(string data, string key)
{
	System.Text.ASCIIEncoding encoding = new System.Text.ASCIIEncoding();
	byte[] keybytes = encoding.GetBytes(key);
	byte[] databytes = encoding.GetBytes(data);
	System.Security.Cryptography.HMACSHA1 hmac1 = new System.Security.Cryptography.HMACSHA1(databytes);
	System.Security.Cryptography.CryptoStream cs1 = new System.Security.Cryptography.CryptoStream(
		System.IO.Stream.Null, hmac1, System.Security.Cryptography.CryptoStreamMode.Write);
	cs1.Write(keybytes, 0, keybytes.Length);
	cs1.Close();
	byte[] result1 = hmac1.Hash;
	return ByteToHex(result1).ToUpper();
}

private static string ByteToHex(byte[] b)
{
	StringBuilder converted = new StringBuilder();
	for (int i = 0; i <= b.Length - 1; i++)
	{
		string hex = Conversion.Hex(b[i]);
		if (hex.Length == 1) converted.Append("0" + hex);
		else converted.Append(hex);
	}
	return converted.ToString();
}
```

## PartnerChallenge

- Preconditon: An accesstoken has been retrieved using the [authentication](/authentication.md) methods on your partner ExorLive user ID.

- Http-POST

Use this method to get the "salt" (we call it a challenge) to use in the password encryption algorithm.

- ApplictionKey: The same key as is used in the authentication process. Is used to identify/verify the application.

- Random: A random integer. Use the same integer when this challenge is used in a PartnerLinkOrganizationToApplication call. Use a different random on subsequent calls 
to PartnerChallenge.

C# sample code:

```c#
string accessToken = GetAccessToken(partnerExorLiveUserId); // Authenticate as this user

// Use the new version 4 of the API (api4)
string reqUri = ExorLiveAppDomain + "/api4/Access/PartnerChallenge";
int random = new Random().Next();

var data = new Dictionary<string, string>
	{
		{"ApplicationKey", ClientKey},
		{"Random", random.ToString()}
	};
var json = MakeJson(data);
string response = SendHttpRequest("POST", reqUri, "application/json", json, accessToken); // Do the call
string challenge = response.Trim(' ', '\"', '{', '}');
```

## PartnerLinkOrganizationToApplication

- Preconditon: An accesstoken has been retrieved using the [authentication](/authentication.md) methods on your partner ExorLive user ID.

- Preconditon: A valid challenge has been retrieved. A challenge is valid for 30 seconds.

- Preconditon: The password is encyprypted as described above.

- Http-POST

Use this method to approve the users organization for API calls from this application.

- ClientKey: The same key as is used in the authentication process. Is used to identify/verify the application.

- Username: The username (usually the email) of an administration user in an organization that we want to access.

- PwdSalted: The encrypted password.

- Challenge: The challenge that was used to encrypt this password.

- Random: The same number that was used when this challenge was retrieved.

Check the HttpResponse. If HttpStatusCode=OK the call was a success, otherwise, check the error message.

A successful response has a content like this:

```c#
{ "resultCode": "OK", "partnerId": 123, "organizationId": 1234, "adminUserId": 12345}
```

Store the adminUserId for use when getting an accesstoken for this organization.

See the sample code to the right.

Below is a list of the possible resultCode's that your code should handle:

```json
    "OK":                    /* Success. */

	"MissingInputValues":    /* One or more of the input parameters have blank values. */

	"IllegalApplicationKey": /* The ClientKey does not match the one used when the
	                         accesstoken was issued. */

	"ChallengeError":        /* The challenge wasn't valid. A challenge is valid for 30 seconds 
	                         and must be accompanied by the same random as when it was issued.  */

	"UserAndPwdNotFound":    /* Username or password was invalid. */

	"UserIsDisabled":        /* The user with this username/password is disabled. */

	"UserIsNotAdmin":        /* The user with this username/password is not an administrator user. */

	"UserIsInSubdepartment": /* The user with this username/password does not have administration 
	                         access to the whole organization. */
```

C# sample code:

```c#
// Use SHA1 and salt on the password.
string shaMacPwd = EncryptPassword(password, challenge);

reqUri = ExorLiveAppDomain + "/api4/Access/PartnerLinkOrganizationToApplication";
data = new Dictionary<string, string>
	{
		{"ClientKey", ClientKey},
		{"Username", username},
		{"PwdSalted", shaMacPwd},
		{"Challenge", challenge},
		{"Random", random.ToString()}
	};
json = MakeJson(data);
// Do the call
response = SendHttpRequest("POST", reqUri, "application/json", json, accessToken);
```

## GetUserAccessStatus

- Preconditon: An accesstoken has been retrieved using the [authentication](/authentication.md) methods on your partner ExorLive user ID.

- Http-GET

This is a nice convenience method to check the status of a user Id.

If statusCode=OK this call will return:

```json
{ "userId": 12345, "organizationOK": true, "userOK": true, "userStatus": "Administrator"}
```

UserStatus may be one of the following:

```json
"OrganizationNotConnected"
"UserIsDisabled"
"Administrator"
"Instructor"
"ContactWithLogin"
"Contact"
```

C# sample code:

```c#
// Use the new version 4 of the API (api4)
string reqUri = ExorLiveAppDomain + "/api4/Access/GetUserAccessStatus";

var data = new Dictionary<string, string>
	{
		{"ClientKey", ClientKey},
		{"userId", userId.ToString()}
	};
string parameters = MakeQueryParameters(data);
string response = SendHttpRequest("GET", reqUri, "application/json", parameters, accessToken); // Do the call
```
