---
title: Route with Google
description: Build a driving route with the Google Directions API, and read back Google's own failure codes.
---

# Route with Google

This is [`route/get`](README.md#post-route-get) with the provider pinned to the [Google Directions API](https://developers.google.com/maps/documentation/directions/intro). Use it when the route must come from Google regardless of the account's default provider.

What differs from the generic operation is the failure detail. Google's own status strings arrive in an `errors` array alongside the Navixy platform error code, so a caller can tell a quota problem from an unroutable request.

## API actions

API base path: `/route/google`.

***

{% openapi-operation spec="navixy-platform" path="/route/google/get" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 215 - External service error. The `errors` array holds one of `OVER_QUERY_LIMIT`, when the service has received too many requests from the application within the allowed time period, `REQUEST_DENIED`, when the service denied the application use of the directions service, or `UNKNOWN_ERROR`, a server error that may succeed on retry.
* 218 - Malformed external service parameters. The `errors` array holds one of `NOT_FOUND`, when at least one of the origin, destination, or waypoint locations could not be geocoded, `ZERO_RESULTS`, when no route could be found, `MAX_WAYPOINTS_EXCEEDED`, when the request carries too many waypoints, at most 8 plus the origin and destination, or 23 for Google Maps API for Business customers, or `INVALID_REQUEST`, for an invalid parameter or parameter value.
* 236 - Feature unavailable due to tariff restrictions, when at least one tracker in the request has no `routing` tariff feature.

An error response looks like this:

```json
{
  "success": false,
  "status": {
    "code": 215,
    "description": "External service error"
  },
  "errors": ["OVER_QUERY_LIMIT"]
}
```
