# ExorLive Users API

A "user" in this context can be either:

- Instructor: A Professional user that creates exercise programs.

- Administrator: A user with higher privileges than an instructor. Administrates other users.

- Contact With Login: A client/patient which can use the ExorLive GO mobile app.

- Contact: A client/patient without any access. Is just an entry in the contacts-list of the instructor.

## Description

1. Lookup users based on email, customid, personnummer, ssn, etc.

2. Create or update a user in ExorLive.

3. Set the event callback URL.

## Demo

A simple demo-application written in Javascript is found here: [https://exorlive.com/test/UserDemo.html](https://exorlive.com/test/UserDemo.html)

## The endpoints

Each call expects two values in the HTTP header:

- "ExorLive-Client": The "clientname" to be agreed upon with ExorLive AS.

- "Authorization": "Bearer accesstoken"

See the [authorization section](/authentication.md) about how to get the access token.

## SetUser

Call this to create or update a user object.

### Sample input


| Name         | SetUser                                |
|--------------|----------------------------------------|
| Url          | https://exorlive.com/api4/user/setuser |
| Method       | POST                                   |
| contentType  | application/json; charset=utf-8        |
| Sample input: |                                        |

```json
{
  "Id": 12345678,
  "OrganizationId": 123456,
  "DepartmentId": 0,
  "Role": 2,
  "PrimaryContactId": 123456,
  "Firstname": "Test",
  "Lastname": "Testson",
  "Email": "tester@exorlive.com",
  "Address": "",
  "City": "",
  "Zipcode": "",
  "Phone": "",
  "Comment": "",
  "DateOfBirth": "",
  "CustomId": "abcd-123-def-456",
  "OfficialId": "050780-12345",
  "Gender": 1
}
```

## Lookup User

Call this to find one or more user objects.

| Name         | LookupUser                                                                                                                                                |
|--------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Url          | https://exorlive.com/api4/user/lookupuser                                                                                                                 |
| Method       | GET                                                                                                                                                       |
| Input        | type: email\|id\|name\|customid\|officialid text: Text to search for                                                                                      |
| Sample calls | https://exorlive.com/api4/user/lookupuser?type=email&text=vilhelm@exorlive.com https://exorlive.com/api4/user/lookupuser?type=officialid&text=05078012345 |

## Set event callback URL

Specify a URL that will receive http-requests when certain events happens.

| Name         | SetEventCallbackUrl                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|--------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Url          | https://exorlive.com/api4/user/SetEventCallbackUrl                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Method       | GET                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Input        | callbackUrl: must be a valid URL that may receive GET-requests from exorlive.com                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Sample calls | https://exorlive.com/api4/user/SetEventCallbackUrl?callbackUrl=https%3A%2F%2Fmydomain.com%2Fevents%2F                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Remarks      | The URL is stored for the organization of the logged in user and applies to any events for that organization. When an event is triggered in ExorLive, a http-request is fired to the URL registered. ExorLive will append these parameters to the URL: event userId Supported events are: "workoutReady": One or more workouts are ready to be downloaded from ExorLive. You can then use the Workout Exporter API to download it. Example: Callback URL: https://mycomain.com/events/ ExorLive will send: https://mycomain.com/events/?event=workoutReady&userId=1234 Callback URL: https://mycomain.com/events/?source=exorlive ExorLive will send: https://mycomain.com/events/?source=exorlive&event=workoutReady&userId=1234 |