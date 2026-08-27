---
title: Transaction
description: The account's billing history over a period.
---

# Transaction

A transaction is one movement on the account's balance: a payment in, a charge out. This operation reads them over a period, which is what an integration needs to reconcile the Navixy platform's billing against its own records.

The period is capped. A span longer than the `report.max_time_span` value that [`dealer/get_ui_config`](../commons/dealer.md#post-dealer-get_ui_config) reports returns error 211.

## Transaction object

{% openapi-schemas spec="navixy-platform" schemas="Transaction" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/transaction`.

***

_Required sub-user rights:_ `payment_create`.

{% openapi-operation spec="navixy-platform" path="/transaction/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 211 - Requested time span is too big, more than [report.maxTimeSpan](../commons/dealer.md).

