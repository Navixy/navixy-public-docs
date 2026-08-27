---
title: Counters
description: Odometer and engine-hour counters on a tracker, and the values they hold.
---

# Counters

A counter accumulates a quantity for a tracker over its lifetime, such as distance travelled or engine running time. Unlike a sensor reading, which reports what is true now, a counter carries a running total that can be corrected when it drifts from the vehicle's own instrument.

Counters split into two things, and the operations follow that split. The counter itself is the entity and its multiplier, read and changed with `counter/read` and `counter/update`. Its value is separate, read and set through `counter/value`. For a worked example, see [how to retrieve sensor and counter data](../../../guides/data-retrieval/sensor-data.md).

## API actions

Counter entities live under `/tracker/counter`, and their values under `/tracker/counter/value`. One call, `get_counters`, sits at the tracker root instead.

***

{% openapi-operation spec="navixy-platform" path="/tracker/counter/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.

***

{% openapi-operation spec="navixy-platform" path="/tracker/counter/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - `sensor_id` was given for a counter whose `type` is not `odometer`, or the sensor is not a metering sensor, or it belongs to another tracker.
* 8 - The queue service could not accept the change, retry later.
* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.

***

{% openapi-operation spec="navixy-platform" path="/tracker/get_counters" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/counter/value/get" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

{% openapi-operation spec="navixy-platform" path="/tracker/counter/value/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - One of the counters does not exist or has no values yet.
* 217 - The list names trackers that do not exist or are blocked.

***

{% openapi-operation spec="navixy-platform" path="/tracker/counter/value/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 8 - The queue service could not accept the change, retry later.
* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.

***

{% openapi-operation spec="navixy-platform" path="/tracker/counter/data/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 7 - `from` is after `to`, or the period exceeds 31 days. Reported as error 7 rather than the more general error 211.
* 204 - No such tracker or counter.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 211 - The period is longer than the Navixy platform allows.
