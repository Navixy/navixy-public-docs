---
title: Bill
description: An invoice raised against the account, and the payment link that settles it.
---

# Bill

A bill is an invoice raised against the account. Creating one returns a link the user follows to pay it, so an integration can put a payment step in its own interface rather than sending the user to the Navixy platform.

Both operations here need [plugin](../commons/plugin/README.md) 29 to be available to the user, and the `payment_create` right.

## Bill object

{% openapi-schemas spec="navixy-platform" schemas="Bill" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/bill`.

***

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/bill/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 222 - Plugin not found, when plugin **29** is not available for the user.

***

For a standalone installation, the base part of **link** can be changed with the **billing.orders.baseUrl** config option.

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/bill/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 222 - Plugin not found, when plugin **29** is not available for the user.

