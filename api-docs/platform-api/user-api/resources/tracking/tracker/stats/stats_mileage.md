---
title: Mileage
description: How far a tracker travelled over a period, in kilometres, grouped by tracker and day.
---

# Mileage

Mileage is the distance a tracker covered over a period, returned in kilometres and broken down by tracker and then by day.

Date and time values follow the [platform formats](../../../../../general/api-conventions.md#date-time-formats).

## API actions

API base path: `/tracker/stats/mileage`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/stats/mileage/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 211 - The period is longer than the Navixy platform allows.
* 217 - The list names trackers that do not exist.
* 221 - The dealer's device limit is exceeded.
