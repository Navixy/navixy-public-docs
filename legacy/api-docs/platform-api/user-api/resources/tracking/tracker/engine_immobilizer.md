---
title: Engine immobilizer
description: Read whether a vehicle's engine immobilizer is engaged, and engage or release it.
---

# Engine immobilizer

An engine immobilizer is an electronic security device fitted to a vehicle that prevents the engine from starting, which is what stops a vehicle being hot-wired after someone gets inside. These operations read its current state and change it.

Engaging the immobilizer reaches the vehicle and can prevent it from starting, so the device must be online for the change to take effect.

## API actions

API base path: `/tracker/engine_immobilizer`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/engine_immobilizer/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device does not support an immobiliser.

***

_Required sub-user rights:_ `tracker_set_output`.

{% openapi-operation spec="navixy-platform" path="/tracker/engine_immobilizer/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device does not support this operation.
* 219 - The tracker is a clone.
