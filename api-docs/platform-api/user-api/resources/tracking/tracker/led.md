---
title: LED
description: Read and switch a tracker's indicator LED, on models that expose it.
---

# LED

Some device models expose their indicator LED to the Navixy platform. These operations read whether it is on and switch it.

Only models that expose an LED switch support either call, and switching one reaches the hardware rather than changing a stored setting.

## API actions

API base path: `/tracker/led`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/led/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device does not support this operation.

***

{% openapi-operation spec="navixy-platform" path="/tracker/led/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device does not support this operation.
