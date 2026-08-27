---
title: User events
description: The account's own event history, as opposed to a tracker's.
---

# User events

This operation reads the [event history](README.md) of the account itself: what its users did, rather than what its devices did.

{% hint style="info" %}
A request with an empty body reports `interval` as the missing parameter. There is no such parameter. It is the internal name of the field holding both dates, and the public parameters are `from` and `to`.
{% endhint %}

## API actions

API base path: `/history/user`.

At most one label is added to `message`, as `label: message`. With `add_asset_label` on, an entry naming a vehicle or an employee takes the asset label, which is the vehicle label or, when there is no vehicle, the employee's full name. An entry naming neither falls back to the tracker label, even with `add_tracker_label` off.

***

{% openapi-operation spec="navixy-platform" path="/history/user/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 211 - Requested time span is too big.
* 212 - Requested period is not available on the current plan.
