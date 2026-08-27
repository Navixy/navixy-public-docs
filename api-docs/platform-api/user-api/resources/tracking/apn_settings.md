---
title: APN settings
description: Mobile-internet settings a SIM needs before a device on it can reach the Navixy platform, looked up by phone number.
---

# APN settings

An access point name, or APN, is the mobile-internet configuration a SIM needs before the device holding it can reach the platform. This resource looks those settings up from a phone number.

The lookup is by number rather than by device, so the number does not have to belong to a registered tracker. Settings are resolved against the dealer that the current account belongs to.

## API actions

API base path: `/apn_settings`.

***

{% openapi-operation spec="navixy-platform" path="/apn_settings/read" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - The phone number is not found in the database.
