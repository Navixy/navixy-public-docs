---
title: APN settings by tracker
description: Look up the mobile-internet settings for a registered tracker, by its ID rather than its phone number.
---

# APN settings by tracker

An access point name, or APN, is the mobile-internet configuration a SIM needs before the device holding it can reach the Navixy platform. This operation looks those settings up for a tracker that is already registered.

The difference from [APN settings](../apn_settings.md) is what you have to hand. That one takes a phone number and works for any number, registered or not. This one takes a tracker ID and resolves the number from the tracker.

## API actions

API base path: `/tracker/apn_settings`.

***

_Required sub-user rights:_ `tracker_register`.

{% openapi-operation spec="navixy-platform" path="/tracker/apn_settings/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker, or no APN settings for it.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 214 - The device has no GSM module, or uses a bundled SIM whose details are hidden from the user.
