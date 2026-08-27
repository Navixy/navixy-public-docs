---
title: Engine hours
description: How long a tracker's engine ran over a period, counted per day.
---

# Engine hours

Engine hours are the time a tracker's engine spent running over a period.

The count comes from the device's ignition input, so a model without one answers error 214.

Date and time values follow the [platform formats](../../../../../general/api-conventions.md#date-time-formats).

## API actions

API base path: `/tracker/stats/engine_hours`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/stats/engine_hours/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 211 - The period is longer than the Navixy platform allows.
* 214 - The device has no ignition input.
* 219 - The tracker is a clone.
