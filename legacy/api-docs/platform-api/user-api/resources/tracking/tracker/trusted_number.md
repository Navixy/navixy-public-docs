---
title: Trusted number
description: The phone numbers a device accepts SMS commands from.
---

# Trusted number

A tracker's trusted numbers are the phone numbers trusted to command it by SMS.

The update operation replaces the whole list rather than adding to it, so send every number that should remain trusted and not only the new one. An empty array clears the list.

## API actions

API base path: `/tracker/trusted_number`.

***

{% openapi-operation spec="navixy-platform" path="/tracker/trusted_number/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/trusted_number/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No tracker with this ID belongs to the account.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
