---
title: Delivery session
description: A restricted session that lets an external system follow order and task tracking.
---

# Delivery session

A delivery session is a special session type for integrating order and task tracking into an external system. It is what a courier-tracking page authenticates with, and it carries far less authority than an ordinary session.

The [delivery info](../../../tracking/delivery.md) operations accept this session type alongside the standard one.

## API actions

API base path: `/user/session/delivery`.

***

A demo session may create a delivery session only when one does not already exist.

_Required sub-user rights:_ `delivery_session_create`.

{% openapi-operation spec="navixy-platform" path="/user/session/delivery/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 101 - In demo mode this function is disabled, when the session is a demo one and a delivery session already exists.
* 236 - Feature unavailable due to tariff restrictions.

***

{% openapi-operation spec="navixy-platform" path="/user/session/delivery/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the user has no delivery session.
