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
|Signature	|Task <'HttpResponseMessage'> SetUserProfile(string version, Profile profile)|

## GetWorkoutsForUser

Get all the applicable workouts for the current logged in user.
That includes workoyt templates (that the user has access to) and the personal workouts of the user.

|version      |Should be 4                         |
|-------------|------------------------------------|
| Return value|An array of workout objects.|

|Http method|GET|
|-----------|---|
|Url|api4/client/go/workouts/{version}|
|Signature|HttpResponseMessage GetWorkoutsForUser(string version)|

## GetWorkoutsForUserByTags

Get applicable workouts for the current logged in user, filtered by the tags.

|version|Should be 4|
|-------------|------------------------------------|
| Return value|An array of workout objects.|

|Http method|GET|
|-----------|---|
|Url|api4/client/go/workoutsbytags/{version}|
|Signature|HttpResponseMessage GetWorkoutsForUserByTags(string version, [FromUri] string[] tags)|

## GetActivitiesTodo

Get all activities of the current logged in user that have not been completed yet.

|version|Should be 4|
|-------------|------------------------------------|
| Return value|An array of workout objects.|

|Http method|GET|
|-----------|---|
|Url|api4/client/go/activitiestodo/{version}|
|Signature|HttpResponseMessage GetActivitiesTodo(string version)|

## GetActivitiesTodo

Get all activities of the current logged in user that have not been completed yet.

|version|Should be 4|
|-------------|------------------------------------|
| Return value|An array of activity objects.|

| Http method | GET                                                   |
|-------------|-------------------------------------------------------|
| Url         | api4/client/go/activitiestodo/{version}               |
| Signature   | HttpResponseMessage GetActivitiesTodo(string version) |

## GetActivitiesByWorkout

Get all actvities that are based on the workout with the given ID.

|version|Should be 4|
|-------------|------------------------------------|
| Return value|An array of activity objects.|

| Http method | GET                                                                       |
|-------------|---------------------------------------------------------------------------|
| Url         | api4/client/go/activitiesbyworkout/{version}                              |
| Signature   | HttpResponseMessage GetActivitiesByWorkout(string version, int workoutId) |

## QueryRelatedExercises

Given an exercise, find other exercises that are fairly similar.
Will return a list of exercises. Each will have a rating between 0% and 100% saying how similar the exercise is to the source exercise.
The result is sorted by the rating in descending order. The source exercise first (with rating 100%), then the most similar exercise.
You may probably like to ignore any exercises with rating less than 50%.

### Sample input

```json
{
  SourceMasterExerciseId: 9699,
  WantedCount: 20,
  PackageIds: [12266],
  EquipmentId: 0,
  AsProgression: false,
}
```

| contentType            | application/json; charset=utf-8|
|------------------------|--------------------------------|
| version                | Should be 4|
| SourceMasterExerciseId | Integer. The master id of the source exercise.|
| WantedCount            | The maximum number of related exercises expected in return.|
| PackageIds             | [Optional] An array of ids of exercise packages. If specified, limit the search for related exercises within these exercise packages. |
| EquipmentId            | [Optional] If specified and not 0, limit the search to exercises that use the equipment category with this Id.                        |
| AsProgression          | [Optional] Boolean. If specified and value is 'true', sort the result on level instead of relevance.                                  |
| Return value           | A list of related exercises|

|Http method|GET|
|-----------|---|
|Url|api4/client/go/relatedExercises/{version}|
|Signature|HttpResponseMessage QueryRelatedExercises(string version, [FromUri]RelatedExercisesQuery input)|

## GetWorkout

Get the workout with the given ID.

|version|Should be 4|
|-------------|------------------------------------|
|workoutId|An integer.|
| Return value|A workout object.|

| Http method | GET                                                           |
|-------------|---------------------------------------------------------------|
| Url         | api4/client/go/workout/{version}                              |
| Signature   | HttpResponseMessage GetWorkout(string version, int workoutId) |

## CopyWorkoutToUser

Make a copy of a workout template and assign it to the contact user.

| contentType  | application/json; charset=utf-8                     |
|--------------|-----------------------------------------------------|
| version      | Should be 4                                         |
| input        | An object with two parameters: WorkoutId and UserId |
| Return value | The new workout object.                             |

| Http method | POST                                                                          |
|-------------|-------------------------------------------------------------------------------|
| Url         | api4/client/go/copyworkouttouser/{version}                                    |
| Signature   | HttpResponseMessage CopyWorkoutToUser(string version, CopyWorkoutInput input) |

## SetExercise

Update an existing exercise. Must be an exercise in an activity.
Exercise-properties that have not changed may be skipped in the input.
You may include everything about an exercise that was loaded in an activity call,
but only the properties mentioned in this example may be changed to a different value.

### Sample input
```json
{
  "Id": 123456789,
  "Title": "Squat",
  "Description": "This is the description of how to execute this exercise.",
  "AdditionalInfo": "This is some additional information about this exercise.",
  "Settings":
  [[{
       "key": "sets",
       "name": "Sett",
       "value": 3,
       "unit": ""
  },
  {
       "key": "repetitions",
       "name": "Reps",
       "value": 10,
       "unit": ""
  }]],
  "Completed": false
}
```

| contentType  | application/json; charset=utf-8                                                                       |
|--------------|-------------------------------------------------------------------------------------------------------|
| version      | Should be 4                                                                                           |
| exercise     | An exercise object. Only 'Id' and 'Completed' are mandatory. The rest of the properties are optional. |
| Return value | The complete updated exercise object.                                                                 |

| Http method | POST                                                               |
|-------------|--------------------------------------------------------------------|
| Url         | api4/client/go/exercise/{version}                                  |
| Signature   | HttpResponseMessage SetExercise(string version, Exercise exercise) |

## SetActivity

Save an activity to ExorLive. Either update an existing activity or create a new one.
Use actitivity.Id=0 to create a new activity.
The caller may choose to skip the Guid both for update and creation.
If a Guid (not Guid.Empty) is specified for update, it must match the old guid value or an error is returned.
If a Guid is specified for a creation, it must be a new one. If no Guid is specified for creation, the server will assign a new guid.
See https://exorlive.com/test/GoApiDemo.html "Create new activity JSON based on workout"

| contentType  | application/json; charset=utf-8               |
|--------------|-----------------------------------------------|
| version      | Should be 4                                   |
| activity     | A fully filled out activity object            |
| Return value | The saved activity object (with updated id's) |

| Http method | POST                                                               |
|-------------|--------------------------------------------------------------------|
| Url         | api4/client/go/activity/{version}                                  |
| Signature   | HttpResponseMessage SetActivity(string version, Activity activity) |

## Profile

Most common data about a user profile.
All properties are optional, except Id, when updating a profile.

**Properties**

| Name           | Type     | Description                                                                                                                                                                                                                                                               |
|----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id             | int      | A unique ID for this user instance. Assigned by the server.                                                                                                                                                                                                               |
| CustomId       | string   | An ID defined by an external system when a user profile (a contact / patient) is imported to ExorLive from that external system. ExorLive accepts any string as the 'CustomId' and assumes it is unique within the organization, if not left blank.                       |
| Firstname      | string   | The users firstname.                                                                                                                                                                                                                                                      |
| Lastname       | string   | The users lastname.                                                                                                                                                                                                                                                       |
| Email          | string   | The users email address.                                                                                                                                                                                                                                                  |
| ProfileImageId | int      | The ID of an optional profile image of the user. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_283@100.100.media A value of 0 means no image. |
| UserTags       | string[] | An array of tags that is associated with this user.                                                                                                                                                                                                                       |
| Height         | int      | Hieght of this person in CM. 0 means unknown.                                                                                                                                                                                                                             |
| Weight         | decimal  | The weight of this person in KG. 0 means unknown.                                                                                                                                                                                                                         |
| Gender         | int      | A number representing the gender of this person. 0: unknown 1: male 2: female                                                                                                                                                                                             |
| DateOfBirth    | string   | The birthdate of this person in this format: YYYY-MM-DD. Leave blank if unknown.                                                                                                                                                                                          |
| YearOfBirth    | int      | The birth-year of this person, if not the complete birthdate is known. Is used to know the age of this person, useful for recommending proper exercises. Set to 0 or leave out if not known.                                                                              |

## Workout

A workout that is available to the current logged in user.
May either be a
   'template' - a general workout that is available to many users in the organization.
   'assigned' - a workout assigned to this specific user and probably tailored to this users needs.

   **Properties**

   | Name               | Type           | Description                                                                                                                                                                                                                                                                                                           |
|--------------------|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id                 | int            | A unique ID for this workout instance. Assigned by the server.                                                                                                                                                                                                                                                        |
| Guid               | string         | A "global unique identifier" in this format:    00000000-0000-0000-0000-000000000000                                                                                                                                                                                                                                  |
| Title              | string         | The title of the workout.                                                                                                                                                                                                                                                                                             |
| Description        | string         | Description text, if any.                                                                                                                                                                                                                                                                                             |
| UserId             | int            | Who is this workout assigned to. Or 0 for a template.                                                                                                                                                                                                                                                                 |
| ChangedTimeStamp   | DateTime       | When was last edit of this workout.                                                                                                                                                                                                                                                                                   |
| IsFavorite         | bool           | Is this workout marked as a favorite by the current user. This is the only property that the user may change in the Go client.                                                                                                                                                                                        |
| OriginatingWorkout | int            | Id of the workout this workout was copied from, if any.                                                                                                                                                                                                                                                               |
| ImageId            | int            | The ID of an image that represents the workout. The ID may be a positive or negative number. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_283@100.100.media A value of 0 means no image. |
| Level              | decimal        | Difficulty level 1-6. Easy: 1-2 Medium: 3-4 Hard: 5-6 This is a decimal because it is the average of the level of the individual exercises in the workout.                                                                                                                                                            |
| Exercises          | List<Exercise> | The list of exercises in this workout.                                                                                                                                                                                                                                                                                |
| Tags               | List<string>   | The list of tags associated with this workout.                                                                                                                                                                                                                                                                        |

## Activity

An activity is either a workout or a free session that shall be executed (or has been executed) at a given date and time.
If based on a workout, all workout details (including exercises) are copied to this activity object and might therefore differ from the current version of the workout.

**Properties**

| Name          | Type           | Description                                                                                                                                                                                                                                                                                                                                                                 |
|---------------|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id            | int            | A unique ID for this activity instance. Assigned by the server. When creating a new activity in the client use ID=0, but optionally assign a Guid to it. The server will assign an ID and also a Guid if not already assigned by the client.                                                                                                                                |
| Guid          | string         | A "global unique identifier" in this format:    00000000-0000-0000-0000-000000000000                                                                                                                                                                                                                                                                                        |
| Title         | string         | The title of the activity. Normally the same as the workout or free session it originated from.                                                                                                                                                                                                                                                                             |
| Description   | string         | Normally, the description text from the workout, if any.                                                                                                                                                                                                                                                                                                                    |
| UserId        | int            | The ID of the user that has this activity assigned. I.e. the executer.                                                                                                                                                                                                                                                                                                      |
| StartTime     | DateTime       | Date and time this activity is scheduled.                                                                                                                                                                                                                                                                                                                                   |
| DurationMs    | int            | The duration of this activity, in milliseconds. Convert to a readable value i.e. hours, minutes, seconds when displayed.                                                                                                                                                                                                                                                    |
| Intensity     | int            | A value between 0 and 100. Is used for free sessions. Default is 60. Low: 0-24. Medium: 25-74. High: 75-100.                                                                                                                                                                                                                                                                |
| WorkoutId     | int            | The ID of the workout this activity originated from. Id=0 means that this is a free session.                                                                                                                                                                                                                                                                                |
| FreeSessionId | int            | The Id of the free session. See list of available free sessions.                                                                                                                                                                                                                                                                                                            |
| Completed     | bool           | Boolean value indicating if this activity is executed or not.                                                                                                                                                                                                                                                                                                               |
| Score         | int            | A happiness score (between 0 and 3 included) for the execution of this activity. May be represented by smileys in the GUI. 0: not executed or unknown. 1: Happy 2: Neutral 3: Unhappy - activity didn't go well. Either force the user to set a value, or use happy as default value after an actvity is executed.                                                          |
| ImageId       | int            | The ID of an image that represents the activity. Probably the same image as the Workout it came from. The ID may be a positive or negative number. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_283@100.100.media A value of 0 means no image. |
| Level         | decimal        | Difficulty level 1-6. Easy: 1-2 Medium: 3-4 Hard: 5-6 This is a decimal because it is the average of the level of the individual exercises in the activity.                                                                                                                                                                                                                 |
| Exercises     | List<Exercise> | The list of exercises of this activity, if originated from a workout.                                                                                                                                                                                                                                                                                                       |

## Exercise

An exercise object in a workout or an activity.

**Properties**

| Name           | Type                        | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|----------------|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id             | int                         | A unique ID for this instance of the exercise. Assigned by the server. When creating new exercise instances in the client (as part of an activity), use ID=0.                                                                                                                                                                                                                                                                                                                                                                                                                            |
| MasterId       | int                         | The ID of the master exercise that this exercise originated from. The number of master exercises in ExorLive is about 6000 (as of may 2017).                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Title          | string                      | The name of this exercise.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Description    | string                      | A description of how to execute this exercise. May be a long text.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| AdditionalInfo | string                      | Some exercises has some additonal text that does not belong in the 'Description' field.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Settings       | List<List<ExerciseSetting>> | A group of settings is a called a "round". There might be more than one round of this exercise. Therefore a list of lists. A setting may appear at most once in a round, but the next round is likely to have the same settings but maybe with adjusted values. Note: "sets" and "round" may be equivalent. These two datastructures represent the same:    [[{ sets: 2},{ repetitions: 10}]]    [[{ sets: 1},{ repetitions: 10}],[{ sets: 1},{ repetitions: 10}]]                                                                                                                       |
| Level          | int                         | Difficulty level 1-6. Easy: 1-2 Medium: 3-4 Hard: 5-6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dorsal         | int                         | Anatomy figure back side. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_2001584@100.100.media A value of 0 means no anatomy image.                                                                                                                                                                                                                                                                                                                              |
| Ventral        | int                         | Anatomy figure front side To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_2001585@100.100.media A value of 0 means no anatomy image.                                                                                                                                                                                                                                                                                                                           |
| Image1         | int                         | The first image. The starting position of the exercise. A value of 0 means no image. If the exercise has only one drawing, it is found in Image1 (and Image2=0). To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_283@100.100.media A value of 0 means no image.                                                                                                                                                                                                |
| Image2         | int                         | The ID of the second image. The ending position of the exercise. A value of 0 means no image. If both Image1 and Image2 is set, and you want to display only one image, use Image2 because the ending position describes the exercise best. If only Image2 is set the exercise is described by more than two drawings and they are all gathered in Image2. Then Image2 is wider than normal. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_282@100.100.media |
| HasVideo       | bool                        | Does this exercise have a video available? If so, the URL of the video is:    https://exorlive.com/video/?ex={MasterId}                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Completed      | bool                        | Wether this exercise is executed or not, if it is part of an activity.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Rating         | int                         | A number in the range 0..100. In a related-exercise-search, how related the exercise is to the source exercise.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

## ExerciseSetting

One setting in a series of settings for an exercise.
It has a name, value and unit.
The unit is displayable, except for (time) which means that the value is a time in milliseconds and that the value must be converted to a displayable string.
The properties of this object have lowercase names, by historical reasons.

**Properties**

| Name  | Type   | Description                                                                                              |
|-------|--------|----------------------------------------------------------------------------------------------------------|
| key   | string | A language-independent name for this setting.                                                            |
| name  | string | The displayable name of this setting. Will be in the language of the exercise and workout it belongs to. |
| value | string | The value, usually a number, but may be any string entered by the user.                                  |
| unit  | string | The unit of the value. Displayable in the current language, in all cases except "(time)".                | 