---
title: Alarm mode
description: Turn a tracker's alarm mode on or off, and read whether it is currently on.
---

# Alarm mode

Alarm mode is a per-tracker switch held on the device itself. These two operations read it and change it.

Both depend on the hardware. A model that has no alarm mode answers error 214, and because setting the mode reaches the device, an offline tracker answers error 213.

## API actions

API base path: `/tracker/alarm_mode`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/alarm_mode/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device does not support alarm mode.

***

{% openapi-operation spec="navixy-platform" path="/tracker/alarm_mode/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device does not support alarm mode.
* 219 - The tracker is a clone.
