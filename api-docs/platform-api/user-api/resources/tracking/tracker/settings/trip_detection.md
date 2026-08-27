---
title: Parking detection
description: How the Navixy platform decides a tracker has stopped, which is what splits a trajectory into trips.
---

# Parking detection

Parking detection is how the platform decides that a tracker has stopped moving. That decision does two jobs:

* It splits a continuous trajectory into separate trips, which is what makes a track legible on a map and a tabular report readable.
* It raises the trip start and trip end events that rules can notify on.

The settings here are the thresholds behind that decision, and they are per tracker, because what counts as a stop differs between a delivery van and a crane.

## API actions

API base path: `/tracker/settings/trip_detection`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/settings/trip_detection/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/settings/trip_detection/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
