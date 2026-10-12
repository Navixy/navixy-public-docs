---
title: Route
description: Driving routes between points, built by an external routing provider.
---

# Route

A route is a driving path from a start point to an end point, optionally through waypoints, built by an external routing provider rather than by the Navixy platform itself.

`route/get` uses the account's configured default provider when the request omits `provider_type`. The three provider-specific pages, [Route with Google](route_google.md), [Route with OSRM](route_osrm.md), and [Route with Progorod](route_progorod.md), document the same operation with the provider pinned, and each reports that provider's own failures differently.

Routing is a tariff feature. A request naming a tracker whose plan lacks `routing` returns error 236.

## Route point object

{% openapi-schemas spec="navixy-platform" schemas="RoutePoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Key point object

{% openapi-schemas spec="navixy-platform" schemas="RouteKeyPoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/route`.

***

{% openapi-operation spec="navixy-platform" path="/route/get" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 215 - External service error.
* 218 - Malformed external service parameters.
* 236 - Feature unavailable due to tariff restrictions, when at least one tracker in the request has no `routing` tariff feature.

## More in this section

<!-- endpoint-reference:start -->

#### Route with Google

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/google/get`](route_google.md#post-route-google-get) | POST | Get route with Google |

#### Route with OSRM

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/osrm/get`](route_osrm.md#post-route-osrm-get) | POST | Get route with OSRM |

#### Route with Progorod

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/route/progorod/get`](route_progorod.md#post-route-progorod-get) | POST | Get route with Progorod |

<!-- endpoint-reference:end -->
