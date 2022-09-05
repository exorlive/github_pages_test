<html>
<link rel="stylesheet" href="./index.css">
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

# Link a partner application to ExorLive with JWT

Exorlive would like to make the process of making a link with partner applications as straightforward as possible. Therefore, ExorLive deals internally with the complicated logic of identitfy, creating, and authenticating users.

## Requirements

- The partner application is web based or is able to open and view webpages.

- The partner has a globally unique ID for users and members/patients.

- The partner application is able to sign the request (the data sent to Exorlive) as a signed JSON Web Token (JWT)

## Introduction

This document describes a one way link where the user is logged in to the partner application and has a member/patient/client in focus. The user then clicks the "ExorLive" menu option opening ExorLive in a single sign on manner, after which the member/patient/client is transferred to ExorLive and is set in focus in the ExorLive user interface.

The partner application makes a POST call with all the necessary information to an Exorlive endpoint. This call must include a signed JWT so ExorLive can verify the request. ExorLive responds with a web page. The first time ExorLive sees this user they will be prompted to log in. The next time it will be a single-sign-on.

After the user has logged in the ExorLive webpage is displayed logged in and the member/patient is in focus.

## Response payload
Exorlive can customize the response according to your needs. To get you started we need the following information.

- The name and ID* of the organization. Exorlive needs to know which center/clinic/location the call is coming from, this is what we call an "Organization".

- The name and ID* of the logged in user (instructor).

- The name and ID* of the contact/client/patient.

*ID refers to globally unique IDs from the partner application. ExorLive strongly prefers using SSN, this way it is easier to identify the contact if they were to log in to "ExorLive GO" with Bank ID.

Any extra user data you provide us with will help us improve the user experience (prefilling of emails etc.).

Example response:

```json
{
	"employeeId": "2f3fb098",
	"employeeUsername": "mando",
	"employeeFirstname": "Mando",
	"employeeLastname": "Lorian",
	"employeeEmail": "mando@tatooine.com",
	"organizationName": "Tatooine",
	"organizationId": "ace615c2",
		"contact": {
		"id": "2f3fb098",
		"birthdate": "1969-11-13",
		"ssn": "13116900216",
		"gender": "male",
		"firstname": "Baby",
		"middlename": "",
		"lastname": "Yoda",
		"mobile": "99900000",
		"email": "babyyoda@galaxy.com",
		"diagnosis": "L02",
	}
}
```

### Security

To verify the request, it must be sent as a signed JSON Web Token (JWT). We prefer an asymmetric key, i.e. you sign with a certificate and share the public key with us. Alternatively we use a symmetric key, i.e. we use a key that is shared between you and us exclusively.

### The POST-call

You send a http POST call which must include a JWT-encoded payload. There are two possible approaches to send the POST call:

- The partner application opens a new webpage (in a tab, iframe or webcontrol). In that webpage a form POST is sent to Exorlive and ExorLive will return the content to display in that webpage. The form POST call contains one field called "payload".

- The partner application sends an AJAX POST request with the payload as data. ExorLive will return a URL and the partner application opens the URL in a tag/iframe/webcontrol.

Code examples:

curl
```
curl --location --request POST 'https://sso.exorlive.com/partner/payload' \
--header 'content-type: application/x-www-form-urlencoded' \
--header 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9' \
--data-urlencode 'payload=eyJraWQiOiIxMzI5ODc5NTg0M...JvTTUSMmtA-caZ7wFPd_A'
```

Form POST request in C#

```c#
[Route("example/sampledata")]
[HttpGet]
public string GetExamplePayload()
{
    var payload = new
    {
        organizationId = "ace615c2",
        organizationName = "Tatooine",
        employeeId = "2f3fb098",
        employeeUsername = "mando",
        employeeFirstname = "Mando",
        employeeLastname = "Lorian",
        employeeEmail = "mando@tatooine.com",
        contact = new
        {
            id = "2f3fb098",
            birthdate = "1969-11-13",
            ssn = "13116900216",
            gender = "male",
            firstname = "Baby",
            middlename = "",
            lastname = "Yoda",
            mobile = "99900000",
            email = "babyyoda@galaxy.com"
        }
    };
    // Encode the payload as a signed JSON Web Token (JWT).
    string encodedPayload = EncodeJwt(GetSecurityKey(), payload);
    return encodedPayload;
}
```

Form POST request in JS

```js
<!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8" />
  <title>ExorLive Partner POST sample</title>
  <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
  <script>
    function CallExorLive() {
    	$.ajax({
    		method: "GET",
    		url: "/example/sampledata",
    		cache: false
    	}).done(function (encodedPayload) {
    		var url = "/example/payload";
    		PostSubmit(url, encodedPayload);
    	});
    }
    
    // Do a form POST submit and open a new window with the resulting webpage.
    // Must use FORM SUBMIT instead of AJAX because the result shall be a 
    // new webpage to be opened.
    // This sample will open the resulting webpage in a new tab in the browser.
    //
    // This method based on:
    // https://stackoverflow.com/questions/133925/javascript-post-request-like-a-form-submit
    // https://stackoverflow.com/questions/178964/after-submitting-a-post-form-open-a-new-window-showing-the-result
    
    function PostSubmit(url, data) {
    	var form = $('<form id="formb" target="_blank"></form>');
    	form.attr("method", "post");
    	form.attr("action", url);
    	var field = $('<input />');
    	field.attr("type", "hidden");
    	field.attr("name", "payload");
    	field.attr("value", data);
    	form.append(field);
    	$(document.body).append(form);
    	form.submit();
    }
  </script>
 </head>
<body>
 <h1>ExorLive Partner POST sample</h1>
 <button onclick="CallExorLive();">Send POST data to ExorLive</button>
</body>
</html>
```
</div>
</html>
