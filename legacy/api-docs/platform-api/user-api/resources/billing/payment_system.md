---
title: Payment system
description: The payment providers an account can pay through, and what a given payment would cost.
---

# Payment system

A payment system is a provider the account can pay through. The list operation reports which are available and how each is configured, and the estimate operation reports what a given payment would actually cost, including any provider fee, before the user commits to it.

## Payment system settings object

{% openapi-schemas spec="navixy-platform" schemas="PaymentSystem" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/payment_system`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/payment_system/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/payment_system/estimate/get" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
