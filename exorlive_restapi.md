# ExorLive API

[Back to frontpage](https://exorlive.github.io/github_pages_test/)

## Authentication

To use this API, all calls must be authenticated, i.e. a token in the header must be present.
The API to do this authentication is found here: ExorLive OAuth 2.0 Authentication.

## The methods

- All calls should have these headers:
- "ExorLive-Client": "*client named to be agreed upon with ExorLive AS.*"
- "Authorization": "*Bearer _accesstoken*"
- The base URL for all these methods is: *https://exorlive.com/*\*

***

[ Base URL: exorlive.com/api4 ]

# User
Gets a user which can be | Instructor | Administrator | Contact with login | Contact
## URL:

  /user/lookupuser

## Method:

  **`GET`**
  
## URL Params:

- type: `email`\|`id`\|`name`\|`customid`\|`officialid` 
- text: `Text to search for`

Sample call: https://exorlive.com/api4/user/lookupuser?`type`=officialid&`text`=05078012345

## Success Response:

  Code: 200 OK
  
  Content: 
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
 
## Status codes / Error codes:

  - 200 OK
  - 401 UNAUTHORIZED - When authentication is required and has failed or has not yet been provided
  - 500 Internal Error - Unexpected condition was encountered and no more specific message is suitable


* **Notes:**

  comments, nice to know etc...