---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:
  - <svg height="18" class="octicon octicon-mark-github" viewBox="0 0 16 16" version="1.1" aria-hidden="true"><path fill="#555" fill-rule="evenodd" d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"></path></svg> <a href='https://github.com/Timetastic/slate'>Edit on Github</a>

search: false
---

# Introduction

👏 Welcome to the Timetastic API 👏

You can use the Timetastic API to integrate Timetastic into other software applications and custom workflows.

The API is designed to be a predictable and intuitive way to interact with your company’s Timetastic account.

Like Timetastic itself the API is under continuous development, so changes and improvements are to be expected. Be sure to keep an eye on our [Changelog](https://changelog.timetastic.co.uk) to receive information on new additions and changes to both Timetastic and the API when we release new features and changes.

**Get started quickly:**

We've created a <a href="Timetastic.postman_collection.json" download>Postman collection of the API endpoints</a> so you can get set up and testing early and easily. You can import this collection into [Postman](https://www.postman.com/), [Insomnia](https://insomnia.rest/), [Paw](https://paw.cloud/) or your HTTP/Rest client of choice.

We've set up environment variables for Timetastic's production API endpoint and commonly used enums in the API. Just add your API key in the environment settings and off you go. 

**Get stuck in:** 

Read the sections below on Authentication and Rate Limiting, then see what you can do!


# Rate Limiting

> Rate limiting responses look like this

```html
Status Code: 429
Retry-After: 58
Content: API calls quota exceeded! maximum admitted 5 per 1s.
```

To keep things running smoothly, there is a rate limit of 5 requests per second, per API key.

Adding, Editing, Archiving and Restoring users has a rate limit of 2 requests per second. 

If you exceed the limit, you'll get a `429` response code.

For successful calls, we set response headers containing information about the rate limit - which can help you monitor your usage.

## Monitoring usage

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
          "dateRangeString": "6 Nov – 8 Nov",
          "startDateString": "Wed 6-Nov-19",
          "endDateString": "Fri 8-Nov-19",
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
**id** | The unique `Id` for this holiday
**dateRangeString** | A string representation of the absence dates
**startDateString** | A string representation of the Start Date
**endDateString** | A string representation of the End Date
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

> List ALL holidays:

```shell
curl "https://app.timetastic.co.uk/api/holidays"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> or to filter for a specific date range:

```shell
curl "https://app.timetastic.co.uk/api/holidays?Start=2018-01-01&End=2018-01-31"
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

`GET https://app.timetastic.co.uk/api/holidays`

### Query Parameters

Append these query parameters to your request to filter your holidays. As a minimum we'd recommend setting a date range filter.

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

You are limited to 100 holidays in any response. If your query returns more than this, you'll need to request any additional pages. With every request, you get a summary as shown in the JSON response in the right-hand column.

By examining this response, you'll be able to determine whether additional requests are needed. The `nextPageLink` will tell you what request to make for the next set of responses.


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

> 🎣 This will raise a Webhook event if you have Webhooks configured

`POST https://api.timetastic.co.uk/api/holidays`

Use this to submit a leave request to Timetastic. You can book for any user in your organisation.
Post your holiday request as JSON in the body of the request.

First of all, you need to specify `from` and `to` dates - these determine the dates for the holiday. If you want from the 1st Jan to the 5th for example, you'd have `2018-01-01` as the `from`, and `2018-01-05` as the `to`.

If you want a booking on just one day, both dates should be set to the same value.


Next, you need to specify a `fromTime` and `toTime`. If you're booking in days (the default in Timetastic), then your options here are `AM` or `PM`.
If you're booking hourly (only supported if the user has their allowances tracked in hours, or if the leave type is non-deductible), then you need to specify the time, expressed as the number of minutes since midnight. For example 540 would represent 9:00am.

Some examples:

from | fromTime | to | toTime | Description
:------: | ---: | :---: | --------: | -----------
2018-01-01 | AM | 2018-01-02 | PM | 2 full days: 1st and 2nd Jan
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

> Assuming this request was valid, here's the response from the server:

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

> Or, if you're already off:

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
**bookAsRequestee** | Bool | No | If set, the request is made as though it were being made by the requestee, instead of the admin user associated with the API key. Use this to ensure the **normal** approval process happens. Defaults to false.

<aside class="warnging">Getting a <strong>415</strong> response code? It's probably that you're not setting the holiday request properly. Check that it's set as JSON in the body of the request, and make sure you set a request header <strong>Content-Type</strong> set to <strong>application/json</strong></aside>


### Holiday Request Response

When you book a holiday through the API you get a JSON response, containing the following information:

Parameter | Description
--------- | -----------
**success** | Whether the booking request was successful or not
**response**  | A response message from the booking request
**rejections** | Whether there were any rejections with the booking, if it was against a department
**autoApproved** | Whether the request was automatically approved or not
**holidayId** | The `Id` of the newly created holiday
**overrideRequired** | Whether the holiday can be force-added (bypassing the max off limit)
**overrideLockedDateRequired** | Whether the holiday can be force-added (overriding any locked dates)

## Action a Holiday
> 🎣 This will raise a Webhook event if you have Webhooks configured

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

`POST https://api.timetastic.co.uk/api/holidays/<ID>`

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
    "managerOfDepartments": [
      10
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://timetasticwebjobsuk.blob.core.windows.net/avatars/u146599.png?v=29",
    "allowanceRemaining": 29.5,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 139422,
    "deptManagerId": 148346,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2017-02-04T00:00:00",
    "hasPublicHolidays": true,
    "userInitials": "MR",
    "countryCode": "GB",
    "currentYearAllowance": 33,
    "nextYearAllowance": 34,
    "yearStart" : 1
  }
```

A user response from the API contains the following information:

Parameter |  Description
--------- | ------- | -----------
**Id** | The unique `Id` for this user
**firstname** | The user's first name
**surname** | The user's surname
**email** | The user's email.
**admin** | Whether this user is an admin
**organisationId** | The organisation Id of this user
**departmentId** | The department Id of this user
**allowanceUnit** | The allowance unit for the user - `Days` or `Hours`.
**managerOfDepartments** | A comma-separated list of department ids that this user is a manager of.
**endDate** | If archived, when the user left the company
**departmentName** | The name of the department this user belongs to
**gravatar** | The URL of the avatar/gravatar resource for this user
**allowanceRemaining** | The allowance remaining for this user, for the current year
**hasLoggedOn** | Whether this user has logged on
**isArchived** | Whether this user is archived
**approverId** | If the user has a specific approver set, this is the Id of that user
**~~deptBossId~~** | We no longer use the word Boss - this parameter may be removed in future, use deptManagerId.
**deptManagerId** | The Id of the user who is the manager of the department this user belongs to
**birthday** | The birthday of this user
**startDate** | The start date for this user.
**hasPublicHolidays** | If this user has public holidays
**userInitials** | The user initials
**countryCode** | The country code of the country this user is assigned to (for public holidays)
**currentYearAllowance** | The allowance for this user, for the current leaveyear
**nextYearAllowance** | The allowance for this user, for the next leave year
**yearStart** | The month that the user's leave year starts (1 being January to 12 being December)


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
    "managerOfDepartments": [
      10
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://timetasticwebjobsuk.blob.core.windows.net/avatars/u146599.png?v=29",
    "allowanceRemaining": 29.5,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 139422,
    "deptManagerId": 148346,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2017-02-04T00:00:00",
    "hasPublicHolidays": true,
    "userInitials": "MR",
    "countryCode": "GB",
    "currentYearAllowance": 33,
    "nextYearAllowance": 34,
    "yearStart": 1
  },
  ...
}
```

List your users, filtered as per your query parameters.

`GET https://app.timetastic.co.uk/api/users`

### Query Parameters

Append these query parameters to your request to filter your users.

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
    "managerOfDepartments": [
      10
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://avatars.timetastic.co.uk/f403c04c-701e-002e-095b-c41ad3000000.png",
    "allowanceRemaining": 29.5,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 139422,
    "deptManagerId": 148346,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2017-02-04T00:00:00",
    "hasPublicHolidays": true,
    "userInitials": "MR",
    "countryCode": "GB",
    "currentYearAllowance": 33,
    "nextYearAllowance": 34,
    "yearStart":1
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


## Get a User & Contact Details
<aside class="notice">This endpoint is only accessible if you have a <a href="https://help.timetastic.co.uk/hc/en-us/categories/360002478198-Timetastic-Pro" rel="noopener" >Timetastic Pro</a> account</aside>


`GET https://app.timetastic.co.uk/api/users/contact/<ID>`


```shell
curl "https://app.timetastic.co.uk/api/users/contact/<ID>"
  -H "Authorization: Bearer YOUR_TOKEN"
```


> The above command returns JSON structured like this:

```json
{
    "jobTitle": "Job Title",
    "payrollId": "P12345",
    "address1": "Suite 143",
    "address2": "19 Lever Street",
    "city": "Manchester",
    "postCode": "M1 1AN",
    "telephone1": "0161 496 0000",
    "telephone2": "07700 900000",
    "country": "GB",
    "emergencyContactName": "Larnell Lewis",
    "emergencyContactPhone": "07700 900001",
    "id": 123456,
    "firstname": "Michael",
    "surname": "League",
    "email": "mleague@example.com",
    "admin": true,
    "organisationId": 123456,
    "departmentId": 78910,
    "allowanceUnit": "Days",
    "managerOfDepartments": [
        60021,
        61175
    ],
    "endDate": null,
    "departmentName": "Customer Service",
    "gravatar": "https://avatars.timetastic.co.uk/f403c04c-701e-002e-095b-c41ad3000000.png",
    "allowanceRemaining": 29.0,
    "hasLoggedOn": true,
    "isArchived": false,
    "approverId": 12345,
    "deptManagerId": 23456,
    "birthday": "1977-02-07T00:00:00",
    "startDate": "2019-01-01T00:00:00",
    "hasPublicHolidays": false,
    "userInitials": "ML",
    "countryCode": null,
    "currentYearAllowance": 29.0,
    "nextYearAllowance": 30.0,
    "userLinkedWithGoogle": false,
    "mfaEnabled": false,
    "inviteSentTimeUtc": null,
    "inviteSentTimeHumanised": null,
    "yearStart": 1
}
```


Retrieves a specific user. In addition to the user detail, we return their work schedule, their allowances and any details from the Contact tab. 

### The user detail response is identical to the <a href="#get-a-specific-user">Get a Specific User</a> endpoint with contact details added:

### Contact Details response

Parameter | Type | Description
--------- | ---- | -----------
**JobTitle** | String | The user's job title
**PayrollId** | String | The user's payroll ID (will be included in reports)
**Address1** | String | First line of the address
**Address2** | String | Address line 2
**City** | String | Address line 3 / City
**PostCode** | String | Address line 4 / PostCode
**Telephone1** | String | Primary contact number
**Telephone2** | String | Secondary contact number
**Country** | Country | String: ALPHA-2 country code | The user's country - set using a 2 letter country code. e.g. GB = United Kingdom
**EmergencyContactName** | String | Name of emergency contact
**EmergencyContactNumber** | String | Contact number of emergency contact


## Add a User
<aside class="notice">⌛ This endpoint is limited to 2 requests per second</aside>

`POST https://app.timetastic.co.uk/api/users/add`


```shell
curl "https://app.timetastic.co.uk/api/users/add"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
  --data $'{
    FirstName: "Charles",
    Lastname: "Babbage",
    Allowance: 25,
    DepartmentId: 123456
}'
```

> The above command returns JSON structured like this:

```json
{
	"id": 123456
}
```
Adds a new user to Timetastic, optionally triggering a welcome email.

### Required Parameters

Parameter | Description
--------- | -----------
FirstName | The user's first name
LastName | The user's last name
Allowance | The user's annual allowance in days
DepartmentId | The ID of the department that this user is in.

### Optional Parameters

Parameter | Type | Description
--------- | ---- | -----------
**EmailAddress** | String | The new user's Email address
**SendWelcomeEmail** | Boolean | Whether to send the user an email to set a password and start using Timetastic
**StartDate** | DateTime | The user's start date. In ISO 8601 format: `2022-02-04T00:00:00`
**leaveYearStart** | Int | The month that the user's leave year starts (1: Jan, 2: Feb&hellip;12: Dec). *Omitting this will create a user with the same leave year that occurs most often in your Organisation*

### Response

Parameter | Type | Description
--------- | ---- | -----------
**Id** | Integer | The new user's ID

### Error Codes
Code | Description
-- | -----------
0 | Succeeded
1 | Succeeded - new start date was set on the user
2 | Failed - something went wrong, check your data is valid
3 | Invalid Value - a value you set is not valid, check names are not blank and that there's a department ID and allowance set. The start date must be before today (if set)
4 | NothingToDo - No action could be completed
5 | Not Permitted - You don't have permission to add a user
6 | Rate Limited - You have exceeded the rate limit[1]

[1]: To prevent user enumeration attacks if you hit the "Email address is in use" error too many times, you will be prevented from adding or editing any more users for an amount of time. This will apply to all API keys for your organisation and cannot be reset.


## Edit a User
<aside class="notice">⌛ This endpoint is limited to 2 requests per second</aside>

`POST https://app.timetastic.co.uk/api/users/edit/<ID>`


```shell
curl "https://app.timetastic.co.uk/api/users/edit/<ID>"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
  --data $'{
    FirstName: "David",
}'
```

> The above command returns a `200 OK` if the changes were successful
 or a `400 BadRequest` if not

Edits an existing user in Timetastic. Allows you to populate more data into a user record after an initial ADD operation.
You can pass any combination of the following parameters and only those values will be changed. 

### Available Parameters

Parameter | Type | Description
--------- | ---- | -----------
FirstName | String | The user's first name
LastName | String |The user's last name
EmailAddress | String | The User's email address. To clear a user's email address you must pass a blank string to this parameter: ""
DepartmentId | Integer | The ID of the department that this user is in.
StartDate | DateTime | The user's start date
Birthday | DateTime | The user's date of birth
Admin | Boolean | Whether the user is a Timetastic Admin (User must have an email address to set this)
ApproverId | Integer | The user's Approver - set this to 0 to default the user's approver to their department boss
HasPublicHolidays | Bool | Whether the user has public holidays enabled. If you're setting public holidays for a new user or user that's not had public holidays enabled before you'll also need to pass a valid `PublicHolidaySetId` in the same request.
PublicHolidaySetId | Integer | The user's public holiday `SetId`. `HasPublicHolidays` will need to be enabled for this to update the user. [See Public Holidays for details on "sets"](#public-holidays) 

### Pro account optional parameters

If you have [Timetastic Pro](https://help.timetastic.co.uk/hc/en-us/categories/360002478198-Timetastic-Pro), you can also pass the following and store them against the user:

Parameter | Type | Description
--------- | ---- | -----------
PayrollId | String | The user's Payroll ID or Employee number in your other systems
JobTitle | String | The user's job title
Address1 | String | User's Address line 1
Address2 | String | User's Address line 2
City | String | User's City
PostCode | String | User's Postcode
Telephone1 | String | User's first phone number (in Timetastic we display this as Mobile)
Telephone2 | String | User's second phone number (in Timetastic we display this as Landline)
Country | String: ALPHA-2 country code | The user's country - set using a 2 letter country code. e.g. GB = United Kingdom
EmergencyContactName | String | The user's emergency contact name
EmergencyContactPhone | String | The user's emergency contact number

### Response

`200 OK` for successfully updating a user record. Failures will return a `400 Bad request` with some error detail as JSON in the body 

```json
{
	"errorStatus": 2,
	"errorMessage": "First name is required"
}
```

### Error Status

When editing a user you may get an error - in most cases you'll get an error message, but the codes are easier to parse:

ErrorStatus | Description
--------- | -----------
0 | Success
1 | Success - user's start date has been set
2 | Failed - check your data is valid
3 | You can't remove the admin status from this user
4 | You can't remove admin privileges for your own user
5 | The email address couldn't be removed as the user is an approver/department boss/admin
6 | Not Permitted - you're updating something you're not allowed to, check your IDs
7 | No action was performed, there was nothing to do
8 | The email address is invalid,
9 | The Email address is already in use[1]
10 | You can't set this user as an Admin, they have no email address
11 | You have exceeded the rate limit[1]

[1]: To prevent user enumeration attacks if you hit the "Email address is in use" error too many times, you will be prevented from adding or editing any more users for an amount of time. This will apply to all API keys for your organisation and cannot be reset.


## Archive a user
<aside class="notice">⌛ This endpoint is limited to 2 requests per second</aside>

Use this to archive a user from Timetastic. They will be immediately logged out and unable to log in.

`POST https://app.timetastic.co.uk/api/users/archive/<ID>`


```shell
curl "https://app.timetastic.co.uk/api/users/archive/<ID>"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
  --data $'{
    ArchiveDate: "2022-09-09 00:00:00",
}'
```

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the user to archive

### Body Parameters

Parameter | Type | Description
--------- | ---- | -----------
ArchiveDate | DateTime | The Date this user left your organisation. Must be in the past (UTC). Timetastic will display this on the archived user's profile and in reports

### Response

`200 OK` for successfully archiving a user. Failures will return a `400 Bad request` with some error detail as JSON in the body 


## Restore a user
<aside class="notice">⌛ This endpoint is limited to 2 requests per second</aside>


Use this to restore a user from Timetastic's Archive - they will be able to log in and use Timetastic as normal.

`POST https://app.timetastic.co.uk/api/users/restore/<ID>`

```shell
curl "https://app.timetastic.co.uk/api/users/archive/<ID>"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
```

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the user to restore

### Response

`200 OK` for successfully restoring a user. Failures will return a `400 Bad request` with some error detail as JSON in the body 

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
**archived** | Whether the department is archived (deleted)
**userCount** | User count
**maxOff** | The maximum number of staff allowed off at any one time

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
    "managerId": 1001,
    "createdAt": "2010-01-01T00:00:00",
    "updatedAt": "2010-01-01T00:00:00",
    "archived": false,
    "userCount": 9,
    "maxOff": 2,
  }
]
```

## Get a Department

Gets a specific department.

`GET https://app.timetastic.co.uk/api/departments/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the department to retrieve

```shell
curl "https://app.timetastic.co.uk/api/departments/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Add a Department

Adds a new department

`POST https://app.timetastic.co.uk/api/departments/add/`

```shell
curl "https://app.timetastic.co.uk/api/departments/add"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
  --data $'{
    Name: "Customer Support",
    ManagerId: 1234,
}'
```

> The above command returns a `200 OK` if the changes were successful with the new department ID
 or a `400 BadRequest` if not

Adds a new department to Timetastic.

### Required Parameters

Parameter | Type | Description
--------- | ---- | -----------
Name | String | The Department name
ManagerId | Int | The userId of the Manager for this department

### Optional Parameters

Parameter | Type | Description
--------- | ---- | -----------
MaxOff | Int [0-20] | The maximum absent setting for this department


## Update a Department

Updates a department

`POST https://app.timetastic.co.uk/api/departments/edit/<ID>`

```shell
curl "https://app.timetastic.co.uk/api/departments/edit/<ID>"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
  --data $'{
    Name: "Customer Support",
    ManagerId: 1234,
    MaxOff: 1
}'
```

> The above command returns a `200 OK` if the changes were successful
 or a `400 BadRequest` if not

Edits an existing department in Timetastic.

### Available Parameters

Parameter | Type | Description
--------- | ---- | -----------
Name | String | The Department name
ManagerId | Int |The userId of the Manager for this department
MaxOff | Int [0-20] | The maximum number of users that can be absent in this department at once. Set to 0 to disable Maximum absent and have no limit. 

As with the user edit endpoint - pass only the fields you want to update and we'll only update those.

## Delete a Department

Deletes a department from Timetastic

`POST https://app.timetastic.co.uk/api/departments/delete/<ID>`

```shell
curl "https://app.timetastic.co.uk/api/departments/delete/<ID>"
  -H "Authorization: Bearer YOUR_TOKEN"
  -H 'Content-Type: application/json'
  '
```

> The above command returns a `200 OK` if the changes were successful
 or a `400 BadRequest` if not

Deletes a department from Timetastic.  The department must be empty - edit any users with this department ID to allow the department to be deleted.

### Required Parameters

Parameter | Type | Description
--------- | ---- | -----------
ID | Int | The Department ID



# Leave Types

## Leave Type Detail

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
    "color": "#6699cc",
    "calendarVisibility": 1,
    "isPrivate": true
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
**calendarVisibility** | The Visibility/Transparency setting of the leave type
**isPrivate** | Whether the leave type is set to private. Leave types with this set to false are public 

**Calendar Visibility**

Timetastic uses calendar transparency settings supported by the [iCal standard](https://icalendar.org/iCalendar-RFC-5545/3-8-2-7-time-transparency.html). Calendar visibility can be set to either of the following values:

Value |  Description
--------- | -------
**1** | Busy
**2** | Available




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
    "color": "#6699cc",
    "calendarVisibility": 1,
    "isPrivate": true
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

Append these query parameters to your request to filter your public holidays.

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


# Webhooks

Set up Webhooks from Timetastic to push leave events to your own server in near real time.

You'll need to be an Admin user to use the API.  Head to [https://app.timetastic.co.uk/api](https://app.timetastic.co.uk/api) and under the _Webhooks_ section set your server URL in the Webhook address field.

Timetastic will only send events if there's a URL set here and all events are sent via HTTP POST to this URL (including retries of failed attempts).  Your server must be using HTTPS and certificates must be valid.

The table will show webhook events and their status over the last 24 hours. This data is also available via the API so you can monitor webhooks for failures.

Timetastic supports the following Webhook events:

### Events

ID | Event | Description
-- | ----- | -----------
0 | **TestEvent** | A Test event was created from the dashboard. None of the data in this event is real, do not treat this event as real data.
1 | **AbsenceRequested** | Fires whenever an absence is requested by any user for any leave type.
2 | **AbsenceApproved** | When absences are approved either by an approver or an Admin
3 | **AbsenceDeclined** | As above, but when absences are declined
4 | **AbsenceCancelled** | When a user cancels an absence in the future or an approver deletes it
5 | **AbsenceBooked** | When an approver books leave in for a user on their behalf OR an absence doesn't require approval (either because it's switched off, or the user is an Admin)

Webhook events are raised in near real time but _are not guaranteed to be delivered in the order they were generated_ - It's possible you could get an `AbsenceCancelled` before an `AbsenceApproved` for the same booking.

### Payload

> Timetastic will send an HTTP POST to your server in the following format

```shell
Timetastic-Secret: "800dce29-fc6b-420b-8654-0f0170a9c572"
```

```json
 {
  "eventId": 90,
  "eventType": "TestEvent",
  "url": "https://your.server.com/TimetasticListener",
  "recordId": -99999,
  "timestamp": "2019-11-07T10:37:49.609652Z",
  "performingUser": {
    "id": 147708,
    "firstname": "Jimmy",
    "surname": "McNulty",
    "fullname": "Jimmy McNulty"
  },
  "recordData": {
    "id": -99999,
    "dateRangeString": "6 Nov – 8 Nov",
    "startDateString": "Wed 6-Nov-19",
    "endDateString": "Fri 8-Nov-19",
    "startDate": "2019-11-06T00:00:00+00:00",
    "startType": "Afternoon",
    "endDate": "2019-11-08T00:00:00+00:00",
    "endType": "Morning",
    "userId": 147708,
    "userName": "Jimmy McNulty",
    "requestedById": 147708,
    "leaveTypeId": -1,
    "duration": 2.0,
    "deduction": 2.0,
    "actionerId": 147708,
    "createdAt": "2019-11-07T10:37:49.5919019+00:00",
    "updatedAt": "2019-11-07T10:37:49.5922024+00:00",
    "reason": "Webhook Test Absence. Do not process",
    "declineReason": "Holiday Decline Reason",
    "status": "Approved",
    "autoApproved": false,
    "bookingUnit": "Days",
    "leaveType": "Webhook Test. Do not process"
  }
}
```

Parameter |  Description
--------- | -----------
eventId | This event's unique ID (retries will have this same ID)
eventType | The Event Type
uRL | The URL of the destination
recordId | The ID of the absence/holiday associated with this event. Test events will always have a record ID of -99999
timestamp | When the event was raised (UTC)
**performingUser** |  The user that raised the event
id | The User ID
firstname | Their first name
surname | Their surname
fullname | Their Full name
**recordData** | This is the event (absence) data (identical to the Holiday Detail - [see here for details](/api/#holiday-detail))

### Security

Your server must be correctly configured to support HTTPS with a valid server certificate. HTTPS is the only option here and loopback/local addresses aren't supported.

Timetastic will send a header named `Timetastic-Secret` with all requests with a key which can be found in the dashboard. You can use this key to verify the request came from Timetastic. You can immediately invalidate and refresh this secret in the API dashboard and it'll change for all attempts going forward, including retries.

### Delivery & Retries

Timeouts on Webhooks are short to enable high throughput of events to all clients.  It is strongly recommended that you acknowledge events immediately by returning a `200` HTTP code and then process them after acknowledging them.  Timetastic will process events as quickly as possible, be prepared for the influx if your entire company books Christmas off at the same time!

<aside class="notice"><strong>Group Bookings</strong>
 are treated as individual holiday events, so a group booking for 150 users will raise 150 webhook calls to your server at once.
</aside>

Returning any `2xx` status code is considered a success. Failed responses will be retried, exponentially backing off until the event expires (e.g. 1,2,4,8,16&hellip; mins).  If you change the URL in the dashboard, all new events _and_ retries will go to this URL. Events expire 24 hours after they are first raised and are then discarded and purged with no further attempts.  Details on retries and the last response received from your server are available in the API dashboard and via the Webhooks API method below.

<aside class="warning"><strong>Redirects are not supported</strong> <br/>
 A URL redirection or "Not Modified" response is treated as a failure. Timetastic will not honour redirects, any other information returned in the request headers or request body is ignored.
</aside>

### Persistent Failures
Webhook delivery will be disabled if after a period of 24 hours more than 80% of webhook requests have failed. This is to prevent a build up of failing clients as webservers fall out of use and domains change over time.  When webhooks are disabled, all of the administrators on your Timetastic account will be emailed. You can re-enable webhooks in the dashboard.  These thresholds may change, the email and this documentation will include the current thresholds.

### Get Webhook Events

The dashboard will show events raised in the last day.  Timetastic retains 30 days of Webhook event history and you can access this via the API.


`GET https://app.timetastic.co.uk/api/webhooks/list/<daysHistory>`

```shell
curl "https://app.timetastic.co.uk/api/webhooks/list/1"
  -H "Authorization: Bearer YOUR_TOKEN"
```

> The above command returns JSON structured like this:

```json
 {
      "id": 90,
      "organisationId": 36133,
      "recordId": -99999,
      "userId": 147708,
      "retryCount": 0,
      "eventType": 0,
      "expiresAt": "2019-11-08T10:37:50",
      "timestamp": "2019-11-07T10:37:50",
      "isProcessed": true,
      "lastProcessedAt": "2019-11-07T10:38:11",
      "lastResponseCode": "200",
      "lastResponseDetail": "",
      "nextAttempt": "0001-01-01T00:00:00",
      "url": "https://your.server.com/TimetasticListener",
      "type": 0
    }
```


Parameter |  Description
--------- | -----------
ID | The Event ID
OrganisationId | Your Organisation ID
RecordId | The recordID associated with this event
UserID | The UserID that raised this event
RetryCount | How many attempts to deliver this event there have been
EventType | The Event Type ID
ExpiresAt | The Expiry Date and Time (UTC)
Timestamp | When the Event was raised (UTC)
IsProcessed | `true` if your server has responded success, otherwise `false`
LastProcessedAt | The UTC Time that the event was last attempted
LastResponseCode | The HTTP Code we last received from your server (trimmed to 4000 characters)
LastResponseDetail | The HTTP Content we last received from your server
NextAttempt | The scheduled time this event will be next attempted (there may be a short delay if many events are queued).
URL | The URL assigned to this event
Type | Used internally for the type of webhook we're sending. For your requests this will _always_ be 0

