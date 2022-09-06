# The ExorLive Plan Template Client REST Api

All calls in this API must include a header that specifies the name of the caller.
The name must be exactly as agreed upon with ExorLive AS.
The syntax is:

Request Header:
  
- ExorLive-Client: MyClientName

A sample project which demonstrates all the API endpoints is found here:Go API Demo

## GetAllTags

Get all tags for all plan templates available for the current user.

|version|Should be 4|
|-------|-----------|
|Return value|An json list with tag objects.|

| Http method | GET                                            |
|-------------|------------------------------------------------|
| Url         | api4/client/plantemplate/getalltags/{version}  |
| Signature   | HttpResponseMessage GetAllTags(string version) |

## GetPlanTemplates

Get all plan templates or a subset of plan templates that matches the query.
The query is free text search and will search both tags and title of the template.

| version      | Should be 4                  |
|--------------|------------------------------|
| query        | Optional text to search for. |
| Return value | A list of plan templates     |

| Http method | GET                                                                               |
|-------------|-----------------------------------------------------------------------------------|
| Url         | api4/client/plantemplate/getplantemplates/{version}                               |
| Signature   | HttpResponseMessage GetPlanTemplates(string version, [FromUri] string query="") { |

## GetPlanTemplatesByTag

Get all plan templates or a subset of plan templates that matches the given tags.

| version      | Should be 4                         |
|--------------|-------------------------------------|
| tagquery     | One or more tags separated by space |
| Return value | A list of plan templates            |

| Http method | GET                                                                                  |
|-------------|--------------------------------------------------------------------------------------|
| Url         | api4/client/plantemplate/getplantemplatesbytag/{version}                             |
| Signature   | HttpResponseMessage GetPlanTemplatesByTag(string version, [FromUri] string[] tags) { |

## Apply

Apply a plan template to a given user based on a selected pattern.

### Sample input

```json
{
   "UserId":1115968,
   "PlanTemplateId":"1234",
   "AppyToStartDate":"2018-05-14",
   "DistributionType":"AsIs",
   "RepeatNum":1,
   "WeekPattern":null,
   "Explicit":null
}
```

| version      | Should be 4                                                                 |
|--------------|-----------------------------------------------------------------------------|
| input        | Details about what Plan Template to distribute to whom and in what pattern. |
| Return value | Nothing.                                                                    |

| Http method | POST                                                                    |
|-------------|-------------------------------------------------------------------------|
| Url         | api4/client/plantemplate/apply/{version}                                |
| Signature   | HttpResponseMessage Apply(string version, PlanTemplateApplyInput input) |

## PlanTemplateApplyInput

**Properties**

| Name             | Type            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|------------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PlanTemplateId   | int             | The Id that identifies this plan template.                                                                                                                                                                                                                                                                                                                                                                                                                    |
| UserId           | int             | The id of the user that shall receive this plan.                                                                                                                                                                                                                                                                                                                                                                                                              |
| AppyToStartDate  | DateTime        | The date of the first activity in the new plan.                                                                                                                                                                                                                                                                                                                                                                                                               |
| DistributionType | string          | Should be one of these 3 types:     "AsIs": distribute the activities exactly as they are in the plan template, relative to the start date.     "WeekPattern": distribute the activities on the weekdays specified in the pattern. Optionally specify the time of day for each day.     "Explicit": You have full control. Specify the date and time for each activity.                                                                                       |
| RepeatNum        | int             | [Optional] How many times to repeat the plan, if weekPattern is used.                                                                                                                                                                                                                                                                                                                                                                                         |
| WeekPattern      | TimePattern     | [Optional] If DistributionType is "WeekPattern", specify the details here. Sample input "WeekPattern": {    "Weekdays":[    {       "DayName":"Monday",       "Times":["10:00","19:00"]    },{       "DayName":"Wednesday",       "Times":["18:00"]    }] }                                                                                                                                                                                                   |
| Explicit         | ExplicitPattern | [Optional] If DistributionType is "Explicit", specify the details here. Sample input "Explicit": {    "List": [    {       "ActivityId":588266,       "Date":"2018-01-10",       "Time":"18:00"    },{       "ActivityId":588265,       "Date":"2018-06-11",       "Time":"19:00"    },{       "ActivityId":588225,       "Date":"2018-06-12",       "Time":"20:00"    },{       "ActivityId":588226,       "Date":"2018-06-13",       "Time":"21:00"    }] } |

## PlanActivity

A PlanActivity is an activity within a plan. The execution date is flexible and relative to the plans start date.

**Properties**

| Name              | Type    | Description                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id                | int     | A unique ID for this activity instance. Assigned by the server.                                                                                                                                                                                                                                                                                                             |
| Guid              | string  | A "global unique identifier" in this format:    00000000-0000-0000-0000-000000000000                                                                                                                                                                                                                                                                                        |
| Title             | string  | The title of the activity. Normally the same as the workout or free session it originated from.                                                                                                                                                                                                                                                                             |
| Description       | string  | Normally, the description text from the workout, if any.                                                                                                                                                                                                                                                                                                                    |
| RelativeStartDate | int     | The number of days after the plans start date.                                                                                                                                                                                                                                                                                                                              |
| Time              | string  | The time of day for this activity in the format HH:MM                                                                                                                                                                                                                                                                                                                       |
| DurationMs        | int     | The duration of this activity, in milliseconds. Convert to a readable value i.e. hours, minutes, seconds when displayed.                                                                                                                                                                                                                                                    |
| Intensity         | int     | A value between 0 and 100. Is used for free sessions. Default is 60. Low: 0-24. Medium: 25-74. High: 75-100.                                                                                                                                                                                                                                                                |
| WorkoutId         | int     | The ID of the workout this activity originated from. Id=0 means that this is a free session.                                                                                                                                                                                                                                                                                |
| FreeSessionId     | int     | The Id of the free session. See list of available free sessions.                                                                                                                                                                                                                                                                                                            |
| ImageId           | int     | The ID of an image that represents the activity. Probably the same image as the Workout it came from. The ID may be a positive or negative number. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_283@100.100.media A value of 0 means no image. |
| Level             | decimal | Difficulty level 1-6. Easy: 1-2 Medium: 3-4 Hard: 5-6 This is a decimal because it is the average of the level of the individual exercises in the activity.                                                                                                                                                                                                                 |

## PlanTemplate

A plan template is a collection of activities that may be laid out in time by an activity distribution pattern.
A plan template may be used by users to make individual workout plans in their calendars.

**Properties**

| Name        | Type               | Description                                                                                                                                                                                                                                                                                                                 |
|-------------|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id          | int                | A unique ID for this plan template instance. Assigned by the server.                                                                                                                                                                                                                                                        |
| Guid        | string             | A "global unique identifier" in this format:    00000000-0000-0000-0000-000000000000                                                                                                                                                                                                                                        |
| Title       | string             | The title of the plan template.                                                                                                                                                                                                                                                                                             |
| Description | string             | Description text, if any.                                                                                                                                                                                                                                                                                                   |
| ImageId     | int                | The ID of an image that represents the plan template. The ID may be a positive or negative number. To get a URL for this image use:    /media_{ID}@{WIDTH}.{HEIGHT}.media    (The image is scaled to fit within the given rectangle). Example:    https://exorlive.com/media_283@100.100.media A value of 0 means no image. |
| Culture     | string             | The language of this plan in the 5 character OSI-format. E.g: en-US, da-DK, nb-NO, sv-SE                                                                                                                                                                                                                                    |
| Weeks       | int                | How many weeks this plan is for.                                                                                                                                                                                                                                                                                            |
| Sessions    | int                | How many sessions in this plan                                                                                                                                                                                                                                                                                              |
| Days        | int                | The duration of this plan, in days.                                                                                                                                                                                                                                                                                         |
| TagList     | List<string>       | Tags assigned to this plan template.                                                                                                                                                                                                                                                                                        |
| Level       | double             | Difficulty level 1-6. Easy: 1-2 Medium: 3-4 Hard: 5-6 This is a decimal because it is the average of the level of the individual exercises in the plan.                                                                                                                                                                     |
| Activities  | List<PlanActivity> | Details about the sessions in this plan.                                                                                                                                                                                                                                                                                    |