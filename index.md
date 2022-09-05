{% include sidebar_menu.html %}

# Overview

This documentation describes the different ways a partner application may communicate with ExorLive. It is called the "link" between ExorLive and the partner application and the main objective is to allow the partner application to be the "master" of the clients data and ExorLive to be the "slave" that provides exercise data to the partner application.

## What you can do with our API

The ExorLive API gives you access to a multitude of data and functions to modify and access said data. It gives you the possiblity to create users, register workouts, view workout statistics for your users, et cetera. It is also possible to integrate our mobile web application (ExorLive Go), which is a fully fledged user experience for contacts/users. If you wish to have more direct control you can make calls to our [API](/methods) and present the data to your users in your own way. Throughout our documentation you will find code examples that are meant to give you a skeleton to work from.

## Terms and Conditions

To use our APIs you need to enter an API Contract with us.

Terms and Conditions to use our APIs can be found in [ExorLive API Terms](https://support.exorlive.com/hc/en-gb/articles/360002096060-ExorLive-API-Terms-of-Service-the-API-Terms) and [ExorLive Application Developer Policy](https://support.exorlive.com/hc/en-gb/articles/360002096020%22).

## Cases

### Link to the main ExorLive application

The instructor works with a contact in the partner application and from there opens the ExorLive application to create exercise programs and plans for that contact. See the different ways this may be done [here](/link_exorlive_main.md).

### Integrate ExorLive Go by Iframe

The contact uses ExorLive Go to view programs and plans and to record activities.
Integrate our HTML5 single page application into your app.

### Integrate ExorLive Go with your own GUI

The partner application manages the exercise data in its own GUI. Yet, it utilizes the features that the ExorLive engine and database provides. In this case the partner application communicates with ExorLive through an API. This approach requires more development work by the partner, compared to the Iframe approach.
See the methods here.

### Get Exercise Programs from the ExorLive system

Methods for downloading workouts assigned to clients/contacts. Download PDF and link the workouts and store these in our own system. The Workout Exporter API is documented here