<html>
<link rel="stylesheet" href="../index.css">
<div class="index_sidebar">
  <h3>API</h3>
  <a href="https://exorlive.github.io/github_pages_test/">Overview</a> </br>
  <a href="https://exorlive.github.io/github_pages_test/getting_started">Getting started</a> </br>
  <a href="https://exorlive.github.io/github_pages_test/support">Support</a></br>
<h3>Cases</h3>
  <a href="https://exorlive.github.io/github_pages_test/link_exorlive_main">Link main ExorLive app</a> </br>
  <a href="https://exorlive.github.io/github_pages_test/link_exorlive_go">Integrate ExorLive Go</a> </br>
<h3>Details</h3>
  <a href="https://exorlive.github.io/github_pages_test/methods">Methods</a> </br>
  <a href="https://exorlive.github.io/github_pages_test/authentication">Authentication</a> </br>
  <a href="https://exorlive.github.io/github_pages_test/account_management">Account Management</a></br>
  <a href="https://exorlive.github.io/github_pages_test/definitions">Definitions</a></br>
  <a href="https://exorlive.github.io/github_pages_test/workout_exporter">Workout exporter</a></br>
</div>
<div class="content">

# ExorLive Authentication - OAuth 2.0

ExorLive uses OAUTH 2.0 for authentication.

It is important that you have been in contact with [support](/support.md) and have all your credentials in order before you try to make any API calls.

See the code samples for a description of how this may be implemented in a partner application.

There are two main ways to authenticate:

1. With a username and password or OpenID.
This gives access to an API that allows to do anything that may be done in the ExorLive Web Application.
In fact ExorLive itself uses this API.
How to use this is not the scope of this documentation. [Contact us if you want to know more about this](/support.md).

2. With an application-key and application-secret.
This gives access to a different API with limited functionality, but enough functionality to do what many of our link-partners may require.
This API is the scope here, and is used in the code samples.

The OAuth parameter called "redirect_uri" will in our case NOT be used for redirecting, only for authentication. Use the value for RequesterUrl that you receive together with ClientKey and ClientSecret. The three parameters ClientKey, ClientSecret and RequesterUrl are together used to validate the AccessToken request. All three parameters must match exactly as received from ExorLive Support.

The code snippets are written in C# / .NET, but the principles here may be implemented in any language that supports HTTP and JSON strings.

```c#
AuthDomain = "https://auth.exorlive.com";
AppDomain = "https://exorlive.com";

// Suggested scope for this sample
_scope = "read_profile read_workout read_master read_calendar read_contact create_session configure";

/*
	This function can be used to authorize both an admin user and a normal user created by an admin.
	Just make sure to use the matching userId.
*/
private string GetAccessToken(int userId)
{
	// This uses OAuth 2.0 conventions.

	// First get the authcode for the given user
	var data = new Dictionary<String, String>
	{
		{"user_id", userId.ToString()}, // user_id === admin id or contact id
		{"response_type", "code"}, // this is an OAuth2 standard
		{"client_id", ClientKey}, // given by ExorLive support
		{"client_secret", ClientSecret}, // given by ExorLive support
		{"redirect_uri", RequesterUrl}, // given by ExorLive support
		{"scope", _scope}
	};
		
	// Create the Auth-URI
	var reqUri = string.Format("{0}/Providers/OAuth/Authorize.aspx", Authdomain);
		
	// Send a WebRequest and receive a WebResponse.
	var response = WebMsg(reqUri, "GET", data, "application/x-www-form-urlencoded", null);
		
	// Deserialize the JSON to an object with same structure as the JSON object.
	var code = JsonConvert.DeserializeObject<JSonResult>(response).code; 

	// Given the authcode, get the access_token.
	data = new Dictionary<String, String>
	{
		{"grant_type", "authorization_code"},
		{"client_id", ClientKey},
		{"client_secret", ClientSecret},
		{"code", code},
		{"redirect_uri", _requesterDomain}				
	};
	var tokenUrl = String.Format("{0}/Providers/OAuth/Token.ashx", Authdomain);
	response = WebMsg(tokenUrl, "POST", data, "application/x-www-form-urlencoded", null);

	// Deserialize the JSON to a dynamic object
	var tokenresponse = JsonConvert.DeserializeObject<dynamic>(response);
    
	// Get the vital information form the JSON response.
	string RefreshToken = tokenresponse.refresh_token;
	DateTime TokenExpiration = DateTime.UtcNow.AddSeconds(tokenresponse.expires_in);

	// Return the token
	return token.access_token;
}
```

The JSON response with the token might look like this:

```json
{
    "access_token": "AAEAAByQvXG9ToTIGqv...55Jvw",
    "token_type": "bearer",
    "expires_in": "7200",
    "refresh_token": "fYz1!IAAAAPU6P...FA0QLteA",
    "scope": "read_profile read_workout create_session"
}
```

The access-token that you get is valid for 2 hours. You may just ask for a new token each time you need one or you may track its expiration and get a new one then, either from scratch or using the refresh-token. The exact time of the expiration of the token is found in the property tokenresponse.expires_in.

An expired token may be refreshed this way:

```c#
private string RefreshToken { get; set; }
private DateTime TokenExpiration { get; set; }
private string RefreshAccessToken()
{
	if ((!string.IsNullOrWhiteSpace(RefreshToken)) && TokenExpiration < DateTime.UtcNow)
	{
		string postData = String.Format("grant_type=refresh_token&client_id={0}&client_secret={1}&refresh_token={2}&redirect_uri={3}",
			ClientKey,
			ClientSecret,
			RefreshToken,
			HttpUtility.UrlEncode(_requesterDomain)
		);
		var tokenUrl = String.Format("{0}/Providers/OAuth/Token.ashx", Authdomain);
		var response = WebMsg(tokenUrl, "POST", data, "application/x-www-form-urlencoded", null);
		try
		{
			// Deserialize the JSON to a dynamic object
			var tokenresponse = JsonConvert.DeserializeObject<dynamic>(response);

			// Get the vital information form the JSON response.
			RefreshToken = tokenresponse.refresh_token;
			TokenExpiration = DateTime.UtcNow.AddSeconds(tokenresponse.expires_in);

			// Return the token
			return tokenresponse.access_token;
		}
		catch (ArgumentException)
		{
			// This will happen when there is invalid token data.
			RefreshToken = null;
			return null;
		}
	}			
}
```

For all the details, see the downloadable codesamples.
</div>
</html>