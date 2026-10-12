---
title: Route with Progorod
description: Build a driving route with the Progorod router, and read back which point it rejected.
---

# Route with Progorod

This is [`route/get`](README.md#post-route-get) with the provider pinned to the [Progorod router](https://giswiki.tmcrussia.com/index.php?title=%D0%9C%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F). Use it when the route must come from Progorod regardless of the account's default provider.

What differs from the generic operation is the failure detail. The `errors` array identifies which point Progorod rejected and why, down to the position of a bad waypoint.

## API actions

API base path: `/route/progorod`.

***

{% openapi-operation spec="navixy-platform" path="/route/progorod/get" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 215 - External service error.
* 218 - Malformed external service parameters. Each entry in the `errors` array carries `type`, one of `not_set`, `malformed`, and `isolated`, and `point`, one of `start`, `end`, `waypoint`, and `all`. An entry for a waypoint also carries `index`, the position of the bad point in the waypoints array.
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
      "type": "malformed",
      "point": "start",
      "index": 3
    }
  ]
}
```
