# The ExorLive Mobile GO Client REST Api

All calls in this API must include a header that specifies the name of the caller.
The name must be exactly as agreed upon with ExorLive AS.
The syntax is:

Request Header:
  
- ExorLive-Client: MyClientName

A sample project which demonstrates all the API endpoints is found here:Go API Demo

## LoadAll

Get all data about the current logged in user.
That includes profile, workouts and activities.

|version      |Should be 4                         |
|-------------|------------------------------------|
| Return value|An object that contains all the data|

|Http method|GET|
|-----------|-----------------------------------|
|Url|api4/client/go/loadall/{version}
|Signature	|HttpResponseMessage LoadAll(string version)|

## GetUserProfile

Get the user profile of the current logged in user.

|version      |Should be 4                         |
|-------------|------------------------------------|
| Return value|A profile object|

|Http method|GET|
|-----------|-----------------------------------|
|Url|api4/client/go/profile/{version}|
|Signature	|HttpResponseMessage GetUserProfile(string version)|

## SetUserProfile

Update the profile of the current logged in user or another user.

### Sample input

```json
{
  "Id": 12345678,
  "CustomId": "x61",
  "Firstname": "Vilhelm",
  "Lastname": "Heiberg",
  "Email": "vilhelm@exorlive.com",
  "ProfileImageId": 0,
  "UserTags": ["TAG1","TAG2"],
  "Height": 180,
  "Weight": 82.5,
  "Gender": 1,
  "DateOfBirth": "1980-11-16",
  "YearOfBirth": 1980
}
```

|contentType|application/json; charset=utf-8|
|-----------|-------------------------------|
|version|Should be 4|
|profile|A profile object.|
|Return value|The same profile object|

|Http method|POST|
|-----------|-----------------------------------|
|Url|api4/client/go/profile/{version}|
|Signature	|Task<HttpResponseMessage> SetUserProfile(string version, Profile profile)|