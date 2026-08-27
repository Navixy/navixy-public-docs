---
title: Retranslator binding
description: Which retranslators forward a given tracker's data, and how to change that.
---

# Retranslator binding

A binding connects one tracker to one [retranslator](../retranslator.md), which is what makes that tracker's data flow on to the third-party server the retranslator points at. The retranslator itself is created separately, and these operations only decide which trackers feed it.

Binding is a master-user action. Creating and removing a binding requires the `admin` right, though listing a tracker's bindings does not.

## Tracker retranslator binding object

{% openapi-schemas spec="navixy-platform" schemas="RetranslatorBinding" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tracker/retranslator`.

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/retranslator/bind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - No such tracker or retranslator.
* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.
* 236 - No tracker has the retranslation tariff feature.
* 242 - `fake_device_id` is invalid for the retranslator's protocol.

***

{% openapi-operation spec="navixy-platform" path="/tracker/retranslator/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 208 - The tracker exists but is blocked, normally because its tariff ended.

***

_Required sub-user rights:_ `admin`, available only to master users.

{% openapi-operation spec="navixy-platform" path="/tracker/retranslator/unbind" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 208 - The tracker exists but is blocked, normally because its tariff ended.
* 219 - The tracker is a clone.
