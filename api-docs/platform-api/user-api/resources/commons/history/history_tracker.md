---
title: Tracker events
description: The event history of one or more trackers over a period.
---

# Tracker events

This operation reads the [event history](README.md) of trackers: everything the Navixy platform recorded about the named devices over a period, including the events its rules raised.

{% hint style="info" %}
A request with an empty body reports `interval` as the missing parameter. There is no such parameter. It is the internal name of the field holding both dates, and the public parameters are `from` and `to`.
{% endhint %}

## API actions

API base path: `/history/tracker`.

***

{% openapi-operation spec="navixy-platform" path="/history/tracker/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 211 - Requested time span is too big.
* 212 - Requested period is not available on the current plan.
* 217 - The list contains non-existent entities.
