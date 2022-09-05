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

# Link to ExorLive Go

ExorLive Go is an HTML5 web app with some limited functionality. For example; it is not possible for the contact to create new exercise programs, only receive them from an instructor.

For all mentioned methods [authentication](/authentication.md) needs to be in place.

## Integrate ExorLive Go in an Iframe

You can integrate [ExorLive Go](/partnerlink_exorlivego.md) in an iframe. This gives the contact the ability to view programs and plans, as well as record workouts.

## Creating your own application
If you wish to create your own application but still have access to the data provided by ExorLive Go, then you can use our [Go API](/goclient_restapi.md) and customize the user experience to your liking.

### Functions available to implement your own mobile application like ExorLive Go

- Methods for creating an end user client.

- Look up available workouts.

- Browse this users activities, including the exercises within activities.

- Create new activities based on available workouts.

- Modify details of activities and their exercises.

- Store execution of activities and exercises.

- Look up available training plans, called "plan templates", and assign them to a users training calender.

A typical use-case for this API is an application for the end user to view his/her training and to record new training sessions.

The general methods are found [here](/goclient_restapi.md).

Methods for the Plan template API are found here.

A demo-application written in Javascript is found here: https://exorlive.com/test/GoApiDemo.html

To use this demo, make sure to be logged in to ExorLive in another tab/window.

</div>
</html>
