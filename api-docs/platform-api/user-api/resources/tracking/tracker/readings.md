---
title: Sensor readings
description: The last value of every sensor, state field and counter on a tracker, in one call.
---

# Sensor readings

These two operations return the last value of everything a tracker measures: metering sensors, discrete inputs, state fields, virtual sensors, and counters, together in one response.

This is the broadest of the reading calls. Where `get_fuel`, `get_readings`, and `get_diagnostics` each cover one slice, `readings/list` covers all of them at once and adds virtual sensors and counters on top. Filtering by `sensor_type` narrows it to matching sensors and drops the state values and counters entirely.

Sensor types are listed under [metering sensor types](sensor/README.md#metering-sensor-type-values) and [virtual sensor types](sensor/README.md#virtual-sensor-type-values). For a worked example, see [how to retrieve sensor and counter data](../../../guides/data-retrieval/sensor-data.md).

## Readings batch object

The `result` object of `readings/batch_list` maps each tracker ID to one readings object:

{% openapi-schemas spec="navixy-platform" schemas="TrackerReadings" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tracker/readings`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/readings/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/readings/batch_list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - The list names trackers that do not exist.
