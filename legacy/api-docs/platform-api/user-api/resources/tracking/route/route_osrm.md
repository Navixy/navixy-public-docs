---
title: Route with OSRM
description: Build a driving route with OSRM, and read back OSRM's own failure detail.
---

# Route with OSRM

This is [`route/get`](README.md#post-route-get) with the provider pinned to the [OSRM API](https://github.com/Project-OSRM/osrm-backend/wiki/Server-api#requesting-routes). Use it when the route must come from OSRM regardless of the account's default provider.

What differs from the generic operation is the failure detail. Each entry in the `errors` array carries OSRM's own status alongside the Navixy platform error code.

## API actions

API base path: `/route/osrm`.

***

{% openapi-operation spec="navixy-platform" path="/route/osrm/get" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 215 - External service error.
* 218 - Malformed external service parameters. Each entry in the `errors` array carries `status`, either `NOT_FOUND`, when at least one of the origin, destination, or waypoint locations could not be geocoded or OSRM cannot find a route, or `UNKNOWN_ERROR`, for an unexpected OSRM code. The entry also carries `status_code`, the OSRM status code, and `message`, the OSRM error message. Don't rely on those last two.
* 236 - Feature unavailable due to tariff restrictions, when at least one tracker in the request has no `routing` tariff feature.

An error response looks like this:

```json
{
  "success": false,
  "status": {
    "code": 218,
    "description": "Malformed external service parameters"
  },
  "errors": [
    {
      "status": "NOT_FOUND",
      "status_code": 207,
      "message": "Cannot find route between points"
    }
  ]
}
```
