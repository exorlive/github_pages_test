# Partner link to ExorLive Go

## ExorLive Go

The version of the ExorLive Web Application that is tailored for the contact user. This version has limited functionality (the contact shall not create exercise programs, only receive them from the instructor). ExorLive Go is an HTML5 web app optimized for mobile browsers.

## The partner system

The link described here applies to a client version the [Partner Application](). This might be a mobile app or a "My Site" version of the company website. The client user of the partner application is logged in and wants to view exercise programs received from an instructor or to record workout activities.

## The link

It is called the "link" between ExorLive and the partner system and the main objective is to allow the partner system to be the "master" of the client's data and ExorLive to be the slave that provides exercise data to the partner system. When the user is logged in in the partner system, it will automatically be taken to the ExorLive Go view without having to log in to ExorLive.

## ExorLive URLS

- auth.exorlive.com - Our authentication server doing OAuth2.0 stuff.

- www.exorlive.com - Our company's main webpage.

- exorlive.com/m/ - ExorLive Go.

- exorlive.com/app/ - The main ExorLive application, for instructors.

- developer.exorlive.com - The site of this documentation.

## Get started

Contact ExorLive Support to get an API key for your ExorLive account. They will also give you the Id of an administration user in that account.

## Open up ExorLive Go already signed in
Overall, follow these steps:

- Get an access-token to use the API, on behalf of the administration user of the account.

- Using the API, look up the contact user within ExorLive, and create it if it wasn't already there.

- Get an access-token for the contact user.

- Compose a URL for ExorLive Go which includes the access-token.

- Open that URL from your Partner Application. The contact will get to the ExorLive Go app already signed in. You may include a skin-code in the URL to get a custom appearance for the ExorLive Go App.

## More details

[Authenticate](/authentication.md) the application, and use the two-step process to get an access-token.

First authenticate using a user-id of an administrator. This will allow access to the [methods](/methods.md) to look up and create users.

Using these methods, you will get the ExorLive User Id of the contact. When this is known, you may use the same authentication process as above to get the access_token of the contact.

With the access_token of the contact, create a URL with the access_token as a parameter to open the webpage of ExorLive Personal with the user logged in.

You may store the access_token for reuse later. It does expire after some time, and it may be renewed with a renewal_token. This is described in the [authentication section](/authentication.md).

The URL is composed like this:

```c#
string redirectUri = "https://exorlive.com/m/?skin=" + skincode;

string uri = String.Format("https://auth.exorlive.com/?access_token={0}&redirect={1}", accessToken, HttpUtility.UrlEncode(redirecturi));
```

You may get the skin code from ExorLive Support, or you may find it yourself in the ExorLive Go-section in the Administration-tab within the main ExorLive application.

## The code

Complete sample code may be [downloaded here](/SampleNativeAppToPersonal.zip).
It is written in C# / .NET but the principles shown here applies to any language supporting HTTP and JSON.
