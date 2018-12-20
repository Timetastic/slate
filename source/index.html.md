---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  
# toc_footers:
#   - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: false
---

# Introduction

You can use the Timetastic API to integrate Timetastic into other software applications and custom workflows.

The API is designed to be a predictable and intuitive way to interact with your company’s Timetastic account.

Like Timetastic itself the API is under continuous development so changes and improvements are to be expected. Be sure to keep an eye on our [Twitter Changelog](https://twitter.com/search?f=tweets&vertical=default&q=timetastic%20%23changelog&src=typd) to receive information on new additions and changes to both Timetastic and the API when we release new features and changes.

To get started, read the sections below on Authentication and Rate Limiting, then get stuck in.


# Rate Limiting

> Rate limiting responses look like this

```html
Status Code: 429
Retry-After: 58
Content: API calls quota exceeded! maximum admitted 5 per 1s.
```

To keep things running smoothly, there is a rate limit of 5 requests per second, per API key. 

If you exceed the limit, the request will fail with a `429` response code.

For successful calls, we set response headers containing information about the rate limit - which can help you monitor your usage. 

### Monitoring usage

For calls that don't get rate limited, you can monitor your usage with the following headers

`X-Rate-Limit-Limit` The rate limit period (eg. 1m, 12h, 1d)

`X-Rate-Limit-Remaining` Number of requests remaining 

`X-Rate-Limit-Reset` UTC date time (ISO 8601) when the limit resets


# Authentication

> To authorize, use this code

```shell
# With shell, you can just pass the correct header with each request
curl "https://app.timetastic.co.uk/api/users"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> Make sure to replace `YOUR_TOKEN` with your API key. 

To use the API, you need an API token. Only admin users have access to an API token.

You can get or renew your API token from Timetastic: [https://app.timetastic.co.uk/api](https://app.timetastic.co.uk/api)

<aside class="notice">The token identifies your user, so you'll have the same permissions as your user account.</aside>


# Holidays

Using the `holidays` endpoint you can query for all types of leave booking in Timetastic. You can also use this endpoint to update (approve or decline) holidays, and to request new time off.

<aside class="success"><strong>Holidays</strong> in the context of this API actually refers to any absence. We’re sorry if this confuses but the API was first built 4 years ago and to update all the endpoints would risk a large amount of breaking changes.</aside>

## Holiday Detail

> A typical holiday response:

```json
      {
          "id": 101010,
          "startDate": "2019-04-11T00:00:00",
          "startType": "Morning",
          "endDate": "2019-04-16T00:00:00",
          "endType": "Afternoon",
          "userId": 121212,
          "userName": "Gareth Terrace",
          "requestedById": 121212,
          "leaveTypeId": 100,
          "duration": 6,
          "deduction": 4,
          "actionerId": 121212,
          "createdAt": "2018-10-07T20:05:02",
          "updatedAt": "2018-10-07T20:05:10",
          "reason": "I'm not working today!",
          "declineReason": null,
          "status": "Approved",
          "autoApproved": false,
          "bookingUnit": "Days",
          "leaveType": "Annual Leave"
      }
```

A holiday response contains the following information:

Parameter |  Description
--------- | ------- | -----------
**Id** | The unique `Id` for this holiday
**startDate** | Start of the holiday. Use in conjunction with the start type to determine the holiday start,
**startType** | **Morning** - Booked for the morning of the start date <br>**Afternoon** - Booked after the morning of the start date (starts at lunchtime) <br>**Hours** - This is an hourly booking, use the time in StartDate
**endDate** | End of the holiday. Use in conjunction with the end type to determine the holiday end
**endType** | Used to define the time the holiday ends: <br>**Morning** - Ends after the morning of the last day (lunchtime) <br>**Afternoon** - Ends at the end of the last day (whole day booked) <br>**Hours** - This is an hourly booking, use the time in the endDate
**userId** | The `Id` of the user this leave is for
**userName** | The full name of the user this leave is for
**requestedById** | The `Id` of the user who requested this leave
**leaveTypeId** | The `Id` of the leave type associated with this leave
**duration** | The full duration of this leave, check `bookingUnit` to determine if it's hours or days
**deduction** | The deduction from the users allowance, check `bookingUnit` to determine if it's hours or days
**actionerId** | If the holiday has been actioned (approved or declined), the `Id` of the user who did this
**createdAt** | When the holiday was created (booked)
**updatedAt** | When the holiday was last updated (including actioning the holiday)
**reason** | The reason for the holiday. Only visible if the requestor has permissions to see this
**declineReason** | The reason for declining (or cancelling) the holiday. Only visible if the requestor has permissions to see this
**status** | The status of this holiday = `['Pending', 'Approved', 'Cancelled', 'Declined']`
**autoApproved** | Whether this holiday was automatically approved (for example, if booked by an Admin user)
**bookingUnit** | Whether the holiday was booked in day(s) or hours. = `['Days', 'Hours']`
**leaveType** | The name of the leave type associated with this leave


## List All Holidays

```shell
curl "https://app.timetastic.co.uk/api/holidays"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> or to filter for a specific date range:

```shell
curl "https://app.timetastic.co.uk/api/holidays?Start=2018-01-01&End=2018-01-31
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
    "holidays": [
        {
            "id": 101010,
            "startDate": "2019-04-11T00:00:00",
            "startType": "Morning",
            "endDate": "2019-04-16T00:00:00",
            "endType": "Afternoon",
            "userId": 121212,
            "userName": "Gareth Terrace",
            "requestedById": 121212,
            "leaveTypeId": 100,
            "duration": 6,
            "deduction": 4,
            "actionerId": 121212,
            "createdAt": "2018-10-07T20:05:02",
            "updatedAt": "2018-10-07T20:05:10",
            "reason": "I'm not working today!",
            "declineReason": null,
            "status": "Approved",
            "autoApproved": false,
            "bookingUnit": "Days",
            "leaveType": "Annual Leave"
        },
        {
            "id": 5256662,
            "startDate": "2019-02-18T00:00:00",
            "startType": "Morning",
            "endDate": "2019-02-22T00:00:00",
            "endType": "Afternoon",
            "userId": 139422,
            "userName": "Gary Bury",
            "requestedById": 139422,
            "leaveTypeId": 100,
            "duration": 5,
            "deduction": 5,
            "actionerId": 139422,
            "createdAt": "2018-11-08T21:40:46",
            "updatedAt": "2018-11-08T21:40:51",
            "reason": "Tererife holiday!",
            "declineReason": null,
            "status": "Approved",
            "autoApproved": false,
            "bookingUnit": "Days",
            "leaveType": "Annual Leave"
        },
        ...
    ]
    "totalRecords": 500,
    "pageNumber": 1,
    "nextPageLink": "https://app.timetastic.co.uk/api/holidays?pagenumber=2",
    "previousPageLink": ""    
}
```

Search for holidays, filtered as per your query parameters.

`GET http://app.timetastic.co.uk/api/holidays`

### Query Parameters

Append these query paramters to your request to filter your holidays. As a minimum we'd recommend setting a date range filter.

Parameter | Default | Description
--------- | ------- | -----------
**DepartmentId** | empty | If set, only include holidays within that department
**UserIds** | empty | If set to an Id (or CSV list of Ids), only include holidays for these users
**ExclusionUserIds** | empty | If set to an Id (or CSV list of Ids), don't include holidays for these users
**NonArchivedUsersOnly** | true | Only return holidays for users that haven't been archived
**Start** | empty | If set, only include holidays that start after this date
**End** | empty | If set, only include holidays that end up to this date
**Status** | PendingOrApproved | Filter all holidays for the given status. If none given then all pending and approved holidays are included. Values are: <br>`Pending (1)` <br> `Approved (2)` <br>`Cancelled (4)` <br> `Declined (8)`. <br>This is a bitwise enum, so for example if you want all declined and cancelled you can pass 12 as the status.
**ApproverId** | empty | If set, only include holidays where this is their approver
**PageNumber** | empty | Used to control paging - see paging below.

### Paging

You are limited to 100 holidays in any response. If your query returns more than this, you'll need to request any additional pages. With every request, you get a sumary as shown in the JSON response in the right-hand column.

By examing this response, you'll be able to determine whether additional requests are needed. The `nextPageLink` will tell you what request to make for the next set of responses.


## Get a Specific Holiday

`GET https://app.timetastic.co.uk/api/holidays/<ID>`

```shell
curl "https://app.timetastic.co.uk/api/holidays/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
  "id": 5256662,
  "startDate": "2019-02-18T00:00:00",
  "startType": "Morning",
  "endDate": "2019-02-22T00:00:00",
  "endType": "Afternoon",
  "userId": 139422,
  "userName": "Gary Bury",
  "requestedById": 139422,
  "leaveTypeId": 100,
  "duration": 5,
  "deduction": 5,
  "actionerId": 139422,
  "createdAt": "2018-11-08T21:40:46",
  "updatedAt": "2018-11-08T21:40:51",
  "reason": "Tererife holiday!",
  "declineReason": null,
  "status": "Approved",
  "autoApproved": false,
  "bookingUnit": "Days",
  "leaveType": "Annual Leave"
}
```

Retrieves a specific holiday.

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the holiday to retrieve

## Request a Holiday

`POST http://api.timetastic.co.uk/api/holidays`

Use this to submit a leave request to Timetastic. You can book for any user in your organisation. 
Post your holiday request as JSON in the body of the request. 

First of all, you need to specify `from` and `to` dates - these determine the dates for the holiday. If you want from the 1st Jan to the 5th for example, you'd have `2018-01-01` as the `from`, and `2018-01-05` as the `to`. 

If you want a booking on just one day, both dates should be set to the same value.


Next, you need to specify a `fromTime` and `toTime`. If you're booking in days (the default in Timetastic), then your options here are `AM` or `PM`. 
If you're booking hourly (only supported if the user has their allowances tracked in hours, or if the leave type is non-deductable), then you need to speciify the time, expressed as the number of minutes since midnight. For example 540 would represent 9:00am.

Some examples:

from | fromTime | to | toTime | Description
:------: | ---: | :---: | --------: | -----------
2018-01-01 &nbsp;&nbsp;&nbsp; | AM | 2018-01-02 &nbsp;&nbsp;&nbsp;&nbsp; | PM | 2 full days: 1st and 2nd Jan
2018-01-01 | AM | 2018-01-01 | PM | 1 full day: 1st Jan
2018-01-01 | AM | 2018-01-02 | AM | Half a day: Just the morning
2018-01-01 | PM | 2018-01-02 | PM | Half a day: Just the afternoon
2018-01-01 | PM | 2018-01-02 | AM | The afternoon of the 1st and the morning of the 2nd
2018-01-01 | 540 | 2018-01-01 | 720 | Hourly booking from 9am to noon

> Let's book off the 20th December, for one user, requested as them:

```shell
curl 'https://app.timetastic.co.uk/api/holidays' 
  -H "Authorization: Bearer YOUR_TOKEN" 
  -H 'Content-Type: application/json'
  --data $'{"from": "2018-12-20",
              "to": "2018-12-20",
              "fromTime": "AM",
              "toTime": "PM",
              "leaveTypeId": 57942,
              "reason": "Testing the API",
              "userOrDepartmentId": 146599,
              "bookFor": 0,
              "suppressEmails": false,
              "override": true,
              "bookAsRequestee": true,
              "overrideLockedDays": true}'
```

> Assumiung this request was valid, here's the response from the server:

```JSON
{
    "success": true,
    "response": "Request sent to Gary Bury",
    "rejections": false,
    "autoApproved": false,
    "holidayId": 5538346,
    "overrideRequired": false,
    "overrideLockedDateRequired": false
}
```

> Or, if you're aleady off:

```JSON
{
    "success": false,
    "response": "You already have an absence booked during this period. Please check the dates.",
    "rejections": true,
    "autoApproved": false,
    "holidayId": 0,
    "overrideRequired": false,
    "overrideLockedDateRequired": false
}
```

### Holiday Request Model Schema

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
**from** | Date string | Yes | The start **date** of the request - don't include time information here.
**to** | Date string | Yes | The end **date** of the request - don't include time information here.
**fromTime** | String | Yes | The start time for the request. Either `AM`, `PM`, or a time. [see above](#request-a-holiday)
**toTime** | String | Yes | The start time for the request. Either `AM`, `PM`, or a time. [see above](#request-a-holiday)
**leaveTypeId** | Integer | Yes | The leave type of the request
**reason** | String | No | A reason for the request
**userOrDepartmentId** | Integer | Yes | The `Id` of the user or department you are booking for. See `bookFor`.
**bookFor** | Enum | Yes | Flag to specify who you are booking for. <br>`0` - Book for the **user** specified in userOrDepartmentId<br>`1` - Book for the **department** specified in userOrDepartmentId<br>`2` - Book for **all users**.
**suppressEmails** | Bool | No | If set, this will prevent any leave request emails from being sent when the request is processed. Defaults to false.
**override** | Bool | No | If set, any department max-off limits are ignored when making the booking. Defaults to false.
**overrideLockedDays** | Bool | No | If set, locked dates are ignored when making the booking. Defaults to false.
**bookAsRequestee** | Bool | No | If set, the request is made as though it were being made by the requestee, instead of the admin user associated with the API key. Use this to ensure the **normal** approvial process happens. Defaults to false.

### Holiday Request Response

When you book a holiday through the API you get a JSON response, containing the following information:

Parameter | Description
--------- | -----------
**success** | Whether the booking request was successfull or not
**response**  | A response message from the booking request
**rejections** | Whether there were any rejections with the booking, if it was against a department
**autoApproved** | Whether the request was automatically approved or not
**holidayId** | The `Id` of the newly created holiday
**overrideRequired** | Whether the holiday can be force-added (bypassing the max off limit)
**overrideLockedDateRequired** | Whether the holiday can be force-added (overriding any locked dates)

## Action a Holiday

> Approve a pending holiday request

```shell
curl 'https://app.timetastic.co.uk/api/holidays/1?holidayUpdateAction=0' 
  -H "Authorization: Bearer YOUR_TOKEN" 
  -H 'Content-Type: application/json'
```

> Decline a pending holiday request 

```shell
curl 'https://app.timetastic.co.uk/api/holidays/1?holidayUpdateAction=1' 
  -H "Authorization: Bearer YOUR_TOKEN" 
  -H 'Content-Type: application/json'
  --data $'{"reason": "Sorry, you're needed in the office."}'
```

`POST http://api.timetastic.co.uk/api/holidays/<ID>`

Use this to action a holiday - approve, decline or cancel. 

### Parameters

Parameter | Parameter Type | Required | Description
--------- | -------------- | -------- | -----------
id | path | Yes | The ID of the holiday to retrieve
holidayUpdateAction | query | Yes | The action you want to perform:<br>`0` - Approve<br>`1` - Decline<br>`2` - Delete
actionModel | body | No | Additional options - see below.

You can pass an optional JSON model in the body of the request to set additional options:

Parameter | Type | Description
--------- | ---- | -----------
reason | String | A reason for declining a request
suppressEmails | Bool | If set, no emails will be sent to the holiday requestee to notify them about the update.

### Holiday Action Response

You'll get a JSON response from the server to let you know the status:

Parameter | Description
--------- | -----------
approvalResult | The result of the action - one of: `['Success', 'AlreadyActioned', 'DoesntExist', 'UnknownError', 'NotAllowed']`
message | Any additional message about the response.

# Users

## User Detail

> A typical user response:

```json
  {
    "id": 100,
    "firstname": "Matt",
    "surname": "Roberts",
    "email": "matt@example.co.uk",
    "admin": true,
    "organisationId": 10,
    "departmentId": 1,
    "allowanceUnit": "Days",
    "bossOfDepartments": [
      10
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://timetasticwebjobsuk.blob.core.windows.net/avatars/u146599.png?v=29",
    "allowanceRemaining": 29.5,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 139422,
    "deptBossId": 148346,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2017-02-04T00:00:00",
    "hasPublicHolidays": true,
    "userInitials": "MR",
    "countryCode": "GB",
    "currentYearAllowance": 33,
    "nextYearAllowance": 34
  }
```

A user response from the API contains the following information:

Parameter |  Description
--------- | ------- | -----------
**Id** | The unique `Id` for this user
**firstname** | The user's firstname
**surname** | The user's surname
**email** | The user's email.
**admin** | Whether this user is an admin
**organisationId** | The organisation Id of this user
**departmentId** | The department Id of this user
**allowanceUnit** | The allowance unit for the user - `Days` or `Hours`.
**bossOfDepartments** | A comma-separated list of department ids that this user is a boss of.
**endDate** | If archived, when the user left the company
**departmentName** | The name of the department this user belongs to
**gravatar** | The URL of the avatar/gravatar resource for this user
**allowanceRemaining** | The allowance remaining for this user, for the current year
**hasLoggedOn** | Whether this user has logged on
**isArchived** | Whether this user is archived
**approverId** | If the user has a specific approver set, this is the Id of that user
**deptBossId** | The Id of the user who is the boss of the department this user belongs to
**birthday** | The birthday of this user
**startDate** | The start date for this user.
**hasPublicHolidays** | If this user has public holidays
**userInitials** | The user initials
**countryCode** | The country code of the country this user is assigned to (for public holidays)
**currentYearAllowance** | The allowance for this user, for the current leaveyear
**nextYearAllowance** | The alloawnce for this user, for the next leave year


## List all Users

```shell
curl "https://app.timetastic.co.uk/api/users"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> or to filter for a specific department:

```shell
curl "https://app.timetastic.co.uk/api/users?departmentId=10
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 100,
    "firstname": "Matt",
    "surname": "Roberts",
    "email": "matt@example.co.uk",
    "admin": true,
    "organisationId": 10,
    "departmentId": 1,
    "allowanceUnit": "Days",
    "bossOfDepartments": [
      10
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://timetasticwebjobsuk.blob.core.windows.net/avatars/u146599.png?v=29",
    "allowanceRemaining": 29.5,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 139422,
    "deptBossId": 148346,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2017-02-04T00:00:00",
    "hasPublicHolidays": true,
    "userInitials": "MR",
    "countryCode": "GB",
    "currentYearAllowance": 33,
    "nextYearAllowance": 34
  },   
  ...
}
```

List your users, filtered as per your query parameters.

`GET http://app.timetastic.co.uk/api/users`

### Query Parameters

Append these query paramters to your request to filter your users.

Parameter | Default | Description
--------- | ------- | -----------
**departmentId** | empty | If set, only include users within that department
**includeArchivedUsers** | empty | If set, archived users are included in the results.
**onlyShowArchivedUsers** | empty | If set, ONLY archived users are included in the results.

## Get a Specific User

`GET https://app.timetastic.co.uk/api/users/<ID>`

```shell
curl "https://app.timetastic.co.uk/api/users/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
"workingDays": [
    {
      "dayOfWeek": "Monday",
      "fromTimeAm": 540,
      "toTimeAm": 720,
      "fromTimePm": 780,
      "toTimePm": 1020,
      "workingAm": true,
      "workingPm": true
    },
    {
      "dayOfWeek": "Tuesday",
      "fromTimeAm": 540,
      "toTimeAm": 720,
      "fromTimePm": 780,
      "toTimePm": 1020,
      "workingAm": true,
      "workingPm": true
    },
    ...
  ],
  "allowances": [
    {
      "year": 2018,
      "allowance": 37,
      "remaining": 29.5,
      "used": -7.5
    },
    {
      "year": 2019,
      "allowance": 34,
      "remaining": 34,
      "used": 0
    }
  ],  
    "id": 100,
    "firstname": "Matt",
    "surname": "Roberts",
    "email": "matt@example.co.uk",
    "admin": true,
    "organisationId": 10,
    "departmentId": 1,
    "allowanceUnit": "Days",
    "bossOfDepartments": [
      10
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://timetasticwebjobsuk.blob.core.windows.net/avatars/u146599.png?v=29",
    "allowanceRemaining": 29.5,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 139422,
    "deptBossId": 148346,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2017-02-04T00:00:00",
    "hasPublicHolidays": true,
    "userInitials": "MR",
    "countryCode": "GB",
    "currentYearAllowance": 33,
    "nextYearAllowance": 34
  }
```

Retrieves a specific user. In addition to the user detail, we return their work schedule, and their allowances.

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the user to retrieve


### Work schedule response

Parameter | Type | Description
--------- | ---- | -----------
**dayOfWeek** | String | The day of the week (e.g. Monday, Tuesday..)
**fromTimeAm** | Integer | The AM from time, expressed as minutes since midnight
**toTimeAm** | Integer | The AM to time, expressed as minutes since midnight
**fromTimePm** | Integer | The PM from time, expressed as minutes since midnight
**toTimePm** | Integer | The PM to time, expressed as minutes since midnight
**workingAm** | Boolean | Whether the user is working AM
**workingPm** | Boolean | Whether the user is working PM

### Allowance response

Parameter | Type | Description
--------- | ---- | -----------
**year** | Integer | The year (taking into account the organisation start date)
**allowance** | Number | The starting allowance, including any days in lieu
**remaining** | Number | The remaining amount
**used** | Number | The used amount

# Departments

## Department Detail

> A typical department response:

```json
  {
    "id": 1,
    "organisationId": 10,
    "name": "Sales",
    "bossId": 1001,
    "createdAt": "2010-01-01T00:00:00",
    "updatedAt": "2010-01-01T00:00:00",
    "givePublicHolidays": false,
    "archived": false,
    "countryCode": "",
    "userCount": 9,
    "currentAllowance": 20,
    "nextAllowance": 35,
    "maxOff": 2,
    "bankHolidaySetId": 0
  }
```

A departments response from the API contains the following information:

Parameter |  Description
--------- | ------- | -----------
**Id** | The unique `Id` for this department
**organisationId** | The organisation Id of this department
**name** | The department's name
**bossId** | The department's boss
**createdAt** | When the department was created
**updatedAt** | When the department was last updated
~~**givePublicHolidays**~~ | OBSOLETE - will be removed
**archived** | Whether the department is archived (deleted)
~~**countryCode**~~ | OBSOLETE - will be removed
**userCount** | User count
**currentAllowance** | The department allowance for the current leave year
**nextAllowance** | The department allowance for the next leave year
**allowanceRemaining** | The allowance remaining for this department, for the current year
**maxOff** | The maximum number of staff allowed off at any one time
~~**bankHolidaySetId**~~ | OBSOLETE - will be removed

## List all Departments

`GET https://app.timetastic.co.uk/api/departments`


Lists all the departments at your organisation

```shell
curl "https://app.timetastic.co.uk/api/departments"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "organisationId": 10,
    "name": "Sales",
    "bossId": 1001,
    "createdAt": "2010-01-01T00:00:00",
    "updatedAt": "2010-01-01T00:00:00",
    "givePublicHolidays": false,
    "archived": false,
    "countryCode": "",
    "userCount": 9,
    "currentAllowance": 20,
    "nextAllowance": 35,
    "maxOff": 2,
    "bankHolidaySetId": 0
  },   
  ...
}
```

## Get a Specific Department

Get's a specific department.

`GET https://app.timetastic.co.uk/api/departments/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the department to retrieve

```shell
curl "https://app.timetastic.co.uk/api/departments/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

# Leave Types

## Department Detail

> A typical leave type response:

```json
  {
    "id": 100,
    "name": "Annual Leave",
    "organisationId": 1,
    "deducted": true,
    "requiresApproval": true,
    "active": true,
    "createdAt": "2016-03-21T10:25:44",
    "updatedAt": "2018-07-31T09:50:20",
    "color": "#6699cc"
  }
```

A Leave Types response from the API contains the following information:

Parameter |  Description
--------- | ------- | -----------
**Id** | The unique `Id` for this leave type
**organisationId** | The organisation Id of this leave type
**name** | The leave type's name
**deducted** | Whether this leave type deducts from the user allowance
**requiresApproval** | Whether this leave type requires approval
~~**active**~~ | OBSOLETE - will be removed
**createdAt** | When the leave type was created
**updatedAt** | When the leave type was last updated
**color** | The color of the leave type when used in Timetastic

## List all Leave Types

`GET https://app.timetastic.co.uk/api/leavetypes`

Lists all the Leave Types at your organisation

```shell
curl "https://app.timetastic.co.uk/api/leavetypes"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 100,
    "name": "Annual Leave",
    "organisationId": 1,
    "deducted": true,
    "requiresApproval": true,
    "active": true,
    "createdAt": "2016-03-21T10:25:44",
    "updatedAt": "2018-07-31T09:50:20",
    "color": "#6699cc"
  },
  ...
}
```

## Get a Specific Leave Type

Gets a specific leave type.

`GET https://app.timetastic.co.uk/api/leavetypes/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the leave type to retrieve

```shell
curl "https://app.timetastic.co.uk/api/leavetypes/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

# Public Holidays

## Public Holiday Detail

> A typical public holiday response:

```json
  {
    "id": 9591352,
    "name": "Christmas Day",
    "date": "2012-12-25T00:00:00",
    "organisationId": 100,
    "createdAt": "2014-10-30T10:40:14",
    "updatedAt": "2015-07-20T10:31:53",
    "countryCode": "GB",
    "bankHolidaySetId": 1
  }
```

A user response from the API contains the following information:

Parameter |  Description
--------- | ------- | -----------
**Id** | The unique `Id` for this public holiday
**name** | The name of the holiday
**date** | The public holiday date
**organisationId** | The organisation Id of this public holiday
**createdAt** | When the public holiday was created
**updatedAt** | When the public holiday was last updated
**countryCode** | The country code associated with this public holiday
**bankHolidaySetId** | The "set" (group of public holidays) that this public holiday belongs to


## List all Public Holidays

```shell
curl "https://app.timetastic.co.uk/api/publicholidays"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> or to filter for a specific year:

```shell
curl "https://app.timetastic.co.uk/api/publicholidays?year=2018
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 9591352,
    "name": "Christmas Day",
    "date": "2012-12-25T00:00:00",
    "organisationId": 100,
    "createdAt": "2014-10-30T10:40:14",
    "updatedAt": "2015-07-20T10:31:53",
    "countryCode": "GB",
    "bankHolidaySetId": 1
  },   
  ...
}
```

List your public holidays, filtered as per your query parameters.

`GET http://app.timetastic.co.uk/api/publicholidays`

### Query Parameters

Append these query paramters to your request to filter your public holidays.

Parameter | Default | Description
--------- | ------- | -----------
**countryCode** | empty | An optional country code to filter the public holidays to
**year** | empty | An optional year to filter the public holidays to
**userId** | empty | An optional user id to filter the public holidays to
**bankHolidaySetId** | empty | An optional bank holiday set to filter the public holidays to

## Get a Specific Public Holiday

`GET https://app.timetastic.co.uk/api/publicholidays/<ID>`

```shell
curl "https://app.timetastic.co.uk/api/publicholidays/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
  {
    "id": 9591352,
    "name": "Christmas Day",
    "date": "2012-12-25T00:00:00",
    "organisationId": 100,
    "createdAt": "2014-10-30T10:40:14",
    "updatedAt": "2015-07-20T10:31:53",
    "countryCode": "GB",
    "bankHolidaySetId": 1
  }
```

Retrieves a specific public holiday.

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the public holiday to retrieve