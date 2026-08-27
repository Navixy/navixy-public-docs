---
title: Digital output
description: Switch a device's built-in digital outputs, one at a time or all at once.
---

# Digital output

A digital output is a switch built into the device. These operations change it, which is how an integration turns something on the vehicle on and off remotely.

Two calls do the same job in different ways, and a model's `output_control` field says which one it accepts: `output/set` switches a single output, `output/set_all` sets every output at once and needs an array matching the number of outputs the model has. Both reach the hardware, so the device must be online.

An output reserved for the engine-block feature cannot be switched here, for safety. To rename an output rather than switch it, use [`tracker/output/update`](README.md#post-tracker-output-update).

## API actions

API base path: `/tracker/output`.

***

_Required sub-user rights:_ `tracker_set_output`.

{% openapi-operation spec="navixy-platform" path="/tracker/output/set_all" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device does not support this operation.
* 219 - The tracker is a clone.

***

_Required sub-user rights:_ `tracker_set_output`.

{% openapi-operation spec="navixy-platform" path="/tracker/output/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 213 - The device is offline.
* 214 - The device does not support this operation.
* 219 - The tracker is a clone.
