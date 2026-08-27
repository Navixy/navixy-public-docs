---
title: Subscription
description: Recurring payments taken through 2Checkout, listed and cancelled.
---

# Subscription

A subscription is a recurring payment that renews the account's service automatically. These operations list the subscriptions in place and cancel one.

Both work through [2Checkout](https://www.2checkout.com), formerly Avangate, which is why the paths carry `avangate`. Because they reach an external service, both can return error 215 when that service is unavailable.

## API actions

API base path: `/subscription`.

### /subscription/avangate/

Working with [2Checkout](https://www.2checkout.com) (formerly [Avangate](http://www.avangate.com)) subscriptions (renewals).

***

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/subscription/avangate/cancel" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 215 - External service error.

***

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/subscription/avangate/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 215 - External service error.

