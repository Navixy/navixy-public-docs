---
title: Tariff (plan)
description: The service plans a device can be on, and what each one allows.
---

# Tariff (plan)

A tariff is the service plan a device is on. It decides what the device costs, what features it has, how long its history is stored, and how many devices the account may hold.

{% hint style="info" %}
**Tariff and plan are the same thing.** The API uses both words. The path is `/tariff` and the field naming one is `tariff_id`, but the object returned is `Plan` and the operation summaries say "List plans" and "Change tracker plan". The Navixy interface calls it a plan.

This page uses tariff for the resource and `Plan` for the object it returns, matching the [Admin Panel API](../../../../panel-api/resources/tariff.md), which names the same resource.
{% endhint %}

This operation lists the tariffs available to the account. To see or change the tariff of one device, use [tracker tariffs](tariff_tracker.md).

## Plan object

{% openapi-schemas spec="navixy-platform" schemas="Plan" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tariff`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/tariff/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

## More in this section

<!-- endpoint-reference:start -->

#### Tracker tariff

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tariff/tracker/change`](tariff_tracker.md#post-tariff-tracker-change) | POST | Change tracker plan |
| [`/tariff/tracker/list`](tariff_tracker.md#post-tariff-tracker-list) | POST | List plans for tracker |

<!-- endpoint-reference:end -->
