---
title: DELETE /titles/{titleId}/users/{xuid}/activities
description: DELETE /titles/{titleId}/users/{xuid}/activities
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, games, uwp, windows 10, xbox one
ms.author: joanlee
security: public
author: joannaleecy
---

# Delete Activity

Deletes the activity for a user playing a specific title.

This API allows a game client to delete the activity for a user playing a particular title. A title should delete the activity for a user as soon as they leave the multiplayer activity. A user's activity may eventually be cleaned up by a presence check if the title fails to delete the activity.

```http
DELETE /titles/{titleId}/users/{xuid}/activities
```

<a id="uri-parameters"></a>
 
## URI parameters

| Parameter| In | Required | Type| Description|
| --- | --- | --- | --- | --- |
| titleId| path | TRUE | integer | The titleId of the game that the request is being sent for.|
| xuid| path | TRUE | string | The XUID of the user being referred to in the current request. The XUID is represented as a base 10 integer.|
  
<a id="request-header"></a>

## Request header

| Name| Required | Type| Description|
| --- | --- | --- | --- |
| Authorization| TRUE | integer | Caller must supply an XToken in the XBL 3.0 authorization format. For example, Authorization: XBL3.0 x=2553948354698;<encrypted token>)|
| Content-Type| TRUE | string | ContentType must be set to application/json |

<a id="request-body"></a>

## Request body

| Name| Required | Type| Description|
| --- | --- | --- | --- |
| sequenceNumber| TRUE | string | This is a string representation of a monotonically increasing number (ulong) that the client must pass in for each request. It is used to guarantee the ordering of the updates to an activity. A good example of a sequenceNumber can be the unix time of the client since it's guaranteed to be increasing. Must be greater than 0, and cannot be greater than the maximum ulong.|

<a id="responses"></a>

## Responses

| Name| Type| Description|
| --- | --- | --- |
| 204 No Content| | The item was deleted.|
| 400 Bad Request| [ErrorResponse](#errorresponse)| An error occurred while processing the request. Possible error codes: "RequestValidationError: The request validation has failed.", " AuthorizationError: The caller is not allowed to access the resource."|
| Other Status Codes| [ThrottledEntity](#throttledentity)| Too many requests. Headers Retry-After: integer|

<a id="definitions"></a>

## Definitions

* [DeleteActivityRequest](#deleteactivityrequest)
* [ErrorResponse](#errorresponse)
* [ThrottledEntity](#throttledentity)

<a id="deleteactivityrequest"></a>

### DeleteActivityRequest

| Name| Type| Description|
|----|----|----|
| sequenceNumber| string | This is a string representation of a monotonically increasing number (ulong) that the client must pass in for each request. It is used to guarantee the ordering of the updates to an activity. A good example of a sequenceNumber can be the unix time of the client since it's guaranteed to be increasing. Must be greater than 0, and cannot be greater than the maximum ulong.|

<a id="errorresponse"></a>

### ErrorResponse

| Name| Type| Description|
|----|----|----|
| debugMessage| string| Human readable version of the error message that can be used for debugging.|
| errorCode| string| Error code enum.|

<a id="throttledentity"></a>

### ThrottledEntity

| Name | Type | Description |
|-------|-----|-----|
| currentRequests| string | Current number of requests - concurrency or rate. |
| debugMessage| string| Human readable version of the throttle message that can be used for debugging.|
| limitType | string | The limit type i.e. concurrency or rate.|
| maxRequests| string| Max request allowed - concurrency or rate. |
| periodInSeconds| string| The period in seconds that applies when rate or concurrency limits are exceeded.|
| version| string| Current throttle contract version.|