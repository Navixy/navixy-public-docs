---
title: Tracking mode
description: How often and under what conditions a device transmits its data.
---

# Tracking mode

Tracking mode is the device's own data transmission and operating configuration: how often it reports, what wakes it up, and how it behaves between reports. It backs the tracking mode settings on the device's settings tab in the interface.

The fields differ from model to model, because they map onto what each device's firmware exposes, so the request and the response are open objects rather than a fixed shape. [Tracking profiles](tracking_profiles.md) lists what a given model accepts. A model with no tracking settings at all answers error 214.

## API actions

API base path: `/tracker/settings/tracking`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/settings/tracking/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - This model has no tracking settings.

***

_Required sub-user rights:_ `tracker_configure`.

{% openapi-operation spec="navixy-platform" path="/tracker/settings/tracking/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - This model has no tracking settings.
* 219 - The tracker is a clone.
