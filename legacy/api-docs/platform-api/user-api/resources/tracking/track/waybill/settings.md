---
title: Waybill settings
description: The waybill number stored by the last download, ready for the next document in the sequence.
---

# Waybill settings

This operation returns the waybill number stored when a waybill was last downloaded. A number consisting only of digits is incremented before being stored, so what comes back is the number the next waybill should carry.

An account that has never downloaded a waybill has no stored number, and the call returns error 201 rather than a default.

## API actions

API base path: `/track/waybill/settings`.

***

{% openapi-operation spec="navixy-platform" path="/track/waybill/settings/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the user has never downloaded a waybill.
