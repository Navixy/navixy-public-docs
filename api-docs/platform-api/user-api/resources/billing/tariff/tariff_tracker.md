---
title: Tracker tariff
description: Which tariff a device is on, and the rules governing a change to another.
---

# Tracker tariff

These operations read the [tariffs](README.md) a device may switch to, and perform the switch.

A change is heavily constrained, and `tariff/tracker/list` exists so a caller can find out what is actually permitted rather than guessing. A user may move a tracker from tariff **t1** to tariff **t2** only when all of the following hold:

1. The tracker belongs to the user and is not a clone.
2. The tracker's tariff was last changed more than `tariff.freeze.period` ago, 30 days by default.
3. `t1.tariff_id` differs from `t2.tariff_id`, so the new tariff is genuinely a different one.
4. `t1.dealer_id` and `t2.dealer_id` both equal the user's effective dealer.
5. `t2.active` is 1, meaning the dealer has allowed users to switch to it themselves.
6. `t1.grouping` equals `t2.grouping`, so the change stays inside one group of tariffs.
7. `t2.device` is `tracker`, so the new tariff is one for trackers.
8. The new tariff is available to the user's legal type. See [Tariff (plan)](README.md).

The **effective dealer** is the user's own dealer when its `dealer_id` equals the configured `defaultDealerId`, or when its `dogovor_type` is `paas`. Otherwise it is that dealer's parent.

## API actions

API base path: `/tariff/tracker`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/tariff/tracker/change" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if user doesn't have trackers with given `tracker_id`.
* 219 - Not allowed for clones of the device.
* 237 - Invalid plan - if there are no plan with specified `tariff_id` and belongs to user's **effective dealer**.
* 221 - Device limit exceeded - when new plan limit for devices is less than the current count of devices in the account.
* 238 - Changing plan is not allowed - user can't switch tracker to that plan.
* 239 - New plan doesn't exist.
* 240 - Not allowed changing plan too frequently - plan last changed less or equal to 30 days (**tariff.freeze.period** config option).

***

Lists the plans a tracker can switch to, even when the plan was last changed less than **tariff.freeze.period** ago.

{% openapi-operation spec="navixy-platform" path="/tariff/tracker/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
