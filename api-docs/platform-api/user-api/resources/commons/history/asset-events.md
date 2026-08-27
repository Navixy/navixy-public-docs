---
title: Asset events
description: The event history of assets over a period, listed or counted.
---

# Asset events

These two operations read the [event history](README.md) of assets: one returns the entries over a period, the other counts them without returning them, which is what a badge or a paging control needs.

The event types that can appear come from [`history/type/list`](history_type.md#post-history-type-list).

## API actions

API base path: `/history/asset`.

`list` adds at most one label to `message`, as `label: message`. With `add_asset_label` on, an entry naming a vehicle or an employee takes the asset label, which is the vehicle label or, when there is no vehicle, the employee's full name. An entry naming neither falls back to the tracker label, even with `add_tracker_label` off.

***

{% openapi-operation spec="navixy-platform" path="/history/asset/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 211 - Requested time span is too big.
* 212 - Requested period is not available on the current plan.

***

{% openapi-operation spec="navixy-platform" path="/history/asset/count" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 211 - Requested time span is too big.
