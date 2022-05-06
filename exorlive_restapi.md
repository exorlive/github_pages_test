# ExorLive REST API

## Authentication

To use this API, all calls must be authenticated, i.e. a token in the header must be present.
The API to do this authentication is found here: ExorLive OAuth 2.0 Authentication.

## The methods

- All calls should have these headers:
  - "ExorLive-Client": "_client named to be agreed upon with ExorLive AS._"
  - "Authorization": "Bearer _accesstoken_"
- The base URL for all these methods is: *https://exorlive.com/*
