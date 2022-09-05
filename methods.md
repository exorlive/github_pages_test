{% include menu.md %}

# ExorLive REST API

## Authentication

To use this API, all calls must be authenticated, i.e. a token in the header must be present. The API to do this authentication is found here: [ExorLive OAuth 2.0 Authentication](/authentication.md).

## The methods

All calls should have these headers:
"ExorLive-Client": "client named to be agreed upon with ExorLive AS."
"Authorization": "Bearer accesstoken"

The base URL for all these methods is: https://exorlive.com/

### Functions available to implement your own mobile application like ExorLive Go

- Methods for creating an end user client.
- Look up available workouts.
- Browse this users activities, including the exercises within activities.
- Create new activities based on available workouts.
- Modify details of activities and their exercises.
- Store execution of activities and exercises.
- Look up available training plans, called "plan templates", and assign them to a users training calender.

 Hence, a typical application to use this API is an application for the end user to view his/her training and to record new training sessions. Similar to the mobile application called "ExorLive Go".

- The general methods are found [here]().

- Methods for the Plan template API are found [here]().

- A demo-application written in Javascript is found here: https://exorlive.com/test/GoApiDemo.html

To use this demo, make sure to be logged in to ExorLive in another tab/window.

### Methods for a journal system or similar

#### The User API - version 4

Methods for creating and looking up users are found [here]().

#### Workout Exporter API

Methods for downloading workouts assigned to clients/contacts. Download PDF and link of the workouts and store these in the our own system. The Workout Exporter API is documented here.

A comprehensive code sample may be downloaded here. It shows the authentication and backend methods, amoung other things. It is well commented code and has more details than found on this webpage.

### Deprecated resources

#### REST API - version 3

The older version of our API can be found here

#### Web-to-web

It's also possible to use our web-to-web solution, but this variant is deprecated and is going to be phased out eventually.
</div>
</html>