# Webwrapper

ExorLive is an application delivered as a single page application, and so in order for desktop applications to efficiently integrate with this, ExorLive provides an executable that wraps the web application in a controllable desktop application. This application is called "ExorLive.Client.WebWrapper", or only "WebWrapper". The WebWrapper executable is a single instance application, and if executed multiple times will pass all arguments to the running instance.

## Installation
An MSI installer is available at https://webwrapper.exorlive.com/. It will install to the default path of "%appdata%\ExorLive\ExorLive Webwrapper" but you can change installation path by running the MSI package using the command line:

```
msiexec /i "ExorLiveWebWrapper.2.0.3.4.msi" INSTALLDIR="C:\myfolder" /q
```

Or you can install it as an [advertised installation](https://msdn.microsoft.com/en-us/library/windows/desktop/aa367548(v=vs.85).aspx), which starts the installation with administrator permissions by the user when he runs the shortcut:

```
msiexec /jm "ExorLiveWebwrapper.2.0.3.4.msi"
```

## Package an installation for other purposes.

It is possible to package the installation for other purposes, ex. by storing it on a CD, or for distribution on a network. You can do this by finding the folder where the program is installed and packaging the content of that folder. You have to create the installation package and create the shortcut to the program yourself.

## Automatic update

The webwrapper application will attempt to check the website webwrapper.exorlive.com at startup to see if there is an update. If so, it will show a download link to the user. This check can be disabled by editing the default configuration file.

## Configuration

The application settings are currently saved in the default configuration file, ExorLive.Client.WebWrapper.exe.config, in the application root. Once a user have launched the application, a second per-user configuration file named "user.config" is created in a subfolder of %localappdata%/ExorLive/.

The following options are available:

- BrowserEngine: Per user setting. Default is EoWebBrowser, but this can be changed to InternetExplorer if you prefer to run the local Internet Explorer engine.
- MinimizeOnExit: Per user setting. Default is True. This will make the WebWrapper stay open in the context menu if the user closes it without signing out. This is to prevent the user having to sign in anew.
- CheckForUpdates: Per user setting. Default is True. This makes the webwrapper check for updates and notify the user if any updates are found.
- ProtocolProvider: Default is ExorLive.Desktop.Exor3.Component, ExorLive.Desktop. This can be modified to use another API protocol provider.
- DistributorName: This should be set to whichever company built the executable. It will get sent back to ExorLive so we can recognize the app (and maybe provide app-specific updates or options).
- Debug: Default is False. Shouldn't need to be changed.
- AppUrl: Default is https://exorlive.com/app. Can be changed to our testing environments or localhost.

# Architecture

Architecturally the WebWrapper do the following:

- Hosts an instance of a Web-browser object displaying the Exor Live web 
application.

- Facilitates internal communication between the WebWrapper application and the Web Application using a COM-API.

- Dynamically loads a protocol provider in order to expose the web application's API to other software. Documented to the right.

The dynamic protocol system enables third-parties to easily integrate the WebWrapper with their systems, using their preferred IPC technology (Named Pipes, TCP, etc), by simply creating a .net component implementing a simple interface. This component can subsequently be loaded dynamically based on the optional configuration file or by passing the name of the provider to the executable using provider="[type]", for example

```c#
provider="ExorLive.Desktop.Arguments.Component, ExorLive.Desktop"
```

The WebWrapper ships with several protocol providers. See details in the "WebWrapper API Interface" section.

## Code and documentation

Sourcecode for the application and latest documentation is available here: bitbucket.org/exorlive/webwrapper

## How to connect to the Webwrapper - The WebWrapper API Interface

Below are documented the general protocols. Either use a commandline interface, an XML interface or a Named Pipe interface. We also have solutions that may be tailored for Active Directory.

## Starting the WebWrapper application from your application

ExorLive Webwrapper is installed to a folder with a unique name in the users local area. To find the path you have to start ExorLive Webwrapper, open the Windows Registry and fin the key 'HKEY_CURRENT_USER\Software\Exor Live\Exor Live Webwrapper\path'. Be aware that webwrapper changes folder when it is updated, så you have to read the registry-key everytime you are looking for the folder.

### ExorLive.Desktop.Arguments

This protocol enables you to repeatedly call the WebWrapper executable with command line arguments in the form of key=value or key="value value".

#### Argument specification
- **queryexercises**: If filled performs a query for exercises.

- **queryworkouts**: If filled performs a query for workouts.

- Selecting a contact, or creates the contact if no contact with that CustomId is found.
    - **id**: Required. The foreign key used to identify this person. Gets saved as "CustomId" in ExorLive.

    - **firstname**: Required. The person's firstname.

    - **lastname**: Required. The person's lastname.

    - **dateofbirth**: Required. The person's date of birth. The date will be parsed using either the ISO 8601 defaults (YYYY-MM-DD), failing that it will attempt to parse it using Windows default culture format.

    - **email**: The person's email address.

- **openworkout**: If filled with a workout id, will tell ExorLive to open that workout.

#### Usage example

```c#
ExorLive.Client.WebWrapper.exe provider="ExorLive.Desktop.Arguments.Component, ExorLive.Desktop" queryexercises="squat" id="user007" firstname="James" lastname="Bond" dateofbirth="1953-04-13"
```

## XML Protocol
When ExorLive Webwrapper uses the XML protocol you can run the webwrapper with an XML file as the argument. See the example below. To run the webwrapper you must first know where it is stored. To find the path you have to start ExorLive Webwrapper, run the Windows Registry and find the key.
This is a file based protocol compatible with Exercise Organizer's "Exor 3" desktop application. Simply create an xml-file "input.xml" according to the following specification and execute the WebWrapper executable with the path to the xml-file as its first argument.

### XML specification

input.xml

```xml
<?xml version="1.0"?>
<exorinput delete="true" exercisequery="" workoutquery="">
<source_customerno>231</source_customerno>
<firstname>Erik</firstname>
<lastname>Røde</lastname>
<born>1911-11-23</born>
<email>erik.viking@mail.com</email>
</exorinput>
```

- delete: Wether to delete the xml-file after reading in. Defaults to true.

- exercisequery: If filled performs a query for exercises.

- workoutquery: If filled performs a query for workouts.

- source_customerno: Required. The foreign key used to identify this person.

- firstname: Required. The person's firstname.

- lastname: Required. The person's lastname.

- born: Required. The person's date of birth, formatted using YYYY-MM-dd.

- email: The person's email address.

**Usage example**

```c#
ExorLive.Client.WebWrapper.exe provider="ExorLive.Desktop.Exor3.Component, ExorLive.Desktop" input.xml
```

## Named Pipes

One way to communicate with the webwrapper is to use Named Pipe communication. This is a new and powerful way of communication introduced summer 2016, and requires an new version (>= 2.1.4.19787) of the Webwrapper installed.

The code of the Webwrapper is open source and found here: https://bitbucket.org/exorlive/webwrapper

Included in this code is a sample client that demonstrate how to use it. If you want to implement Named Pipe communication between your application and ExorLive, please study this code, as it will give you a head start. The Sample Client is called " ExorLiveNamedPipeSampleClient" and is found here: https://bitbucket.org/exorlive/webwrapper/src/b61bcd016aa64a45113fc20fc7ca0cba470732cc/ExorLiveNamedPipeSampleClient/?at=master

The naming of the pipe follows this template:

- "exorlivepipe.[processid]"

- example: "exorlivepipe.12345"

[processid] the process id of the webwrapper process and is used to allowed for this to run with unique names in a terminal server environment. Some sample code about how to get this processid in your application is included in the "ProcessTool" class in the sample client.

### Commands in the Named Pipes API 
**(all commands require that the webwrapper is already running)**

**hide**

Minimize the webwrapperwindow.

**show**

Show the webwrapper window if minimized. Bring the window to the front if it is hidden behind another application.

**version**

Get the webwrapper version.

**close**

Close (exit) the webwrapper application.

**getworkoutsforclient(customid, userid, from)**

customid (string): The id of the user in your system (your id of the user)

userid (integer): The ExorLive id of the user. You may choose to specify either customId or userid in this call.

from (string): Optional. A date in standard parseable format. "yyyy-MM-dd" is preferred. Get only workouts that are newer than this date.

Will return a json string which is an array of workouts with some key information about each workout.

**openworkout(id)**

id (integer): The id of a workout retrieved in 'getworkoutsforclient'

Instruct ExorLive to open and show this workout in its GUI.
getlistofusers(customid)

customid (string): Optional: filter the list of users returned on the custom id specified.

Returns a list of users in your organization, with some properties.

Custom id is not checked for uniqueness by ExorLive, therefore this method may return more than one user even when customid is specified.

**setcustomid(userid, customid)**

customid (string): The id of the user in your system (your id of the user)
userid (integer): The ExorLive id of the user.
Will update the user in ExorLive with your Id, called the CustomId. You need to know the ExorLive User Id first. It may be found using the GetListOfUsers method.

**selectperson(...)**

id: (string): Optional. Obsolete. No longer in use. For backward compatibility, it is interpreted as customid.

customid (string): The id of the user in your system (your id of the user)

userid (integer): The ExorLive id of the user. Use 0 to tell ExorLive this is a new user.

firstname (string):

lastname (string):

email (string): Optional.

address (string): Optional.

phonehome (string): Optional.

phonework (string): Optional.

mobile (string): Optional.

country (string): Optional.

zipcode (string): Optional.

location (string): Optional.

gender (string): Optional. "female" or "male" or "F" or "M"

dateofbirth (string): Optional. Birthdate in yyyy-MM-dd format.

profiledata (array of entries in JSON format):

key (string): Defined By ExorLive, to be able to use this structured data.

name (string): Display name of this data entry. May be the same as key.

value (string): The value of this entry.

unit (string): The unit of this value, if any.

date (string): The date and time of this entry. Format: "yyyy-MM-dd hh:mm:ss" / "yyyy-MM-dd hh:mm" / "yyyy-MM-dd"

source (string): The source of this data.

description (string): Optional text/comment.
... (other unstructured data)

Here is a complete JSON example:

```json
{
	"Method": "SelectPerson",
	"Args": {
		"customid": "12345",
		"firstname": "Ole",
		"lastname": "dole",
		"email": "ole@dole.com",
		"dateOfBirth": "1970-01-01",
		"gender": "M",
		"profiledata": [{
			"key": "diagnosis",
			"name": "Diagnose",
			"value": "Lumbago",
			"date": "2016-08-24 14:04",
			"source": "MyJournal"
		}, {
			"key": "diagnosiscode",
			"name": "Diagnosekode",
			"value": "L2",
			"unit": "ICPC-2",
			"date": "2016-08-24 14:04",
			"source": "MyJournal"
		}, {
			"key": "weight",
			"name": "Vekt",
			"value": "80",
			"unit": "kg",
			"date": "2016-08-23",
			"source": "ExorLive GO",
			"description": "På badevekta om morgenen",
			"customkey": "A Custom Value"
		}]
	}
}
```
