---
title: Status
description: The individual activity states, such as busy or not busy, that a tracker can be set to.
---

# Status

A working status records what an employee is currently doing, tracked through the device they carry. The simplest case is a pair, "busy" and "not busy", and an employee's device is set to one of them at any moment.

Statuses do not stand alone. Each belongs to a [working status list](listing/README.md), and a tracker can only be set to a status from the list assigned to it. Different trackers can carry different lists, so create the list first and add statuses to it with the operations here.

Working statuses are a tariff feature. An account with no tracker whose plan includes `statuses` gets error 236 from every operation on this page. For the task-management side of the same feature, see [how statuses change task states](../../../guides/field-service-management/change-task-statuses.md).

## Working status object

{% openapi-schemas spec="navixy-platform" schemas="WorkingStatus" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/status`.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no list has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.
* 268 - Over quota, when the user's quota for working statuses is exceeded.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no working status has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.

***

{% openapi-operation spec="navixy-platform" path="/status/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no working status has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.

## More in this section

<!-- endpoint-reference:start -->

#### Tracker's working status

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/tracker/assign`](tracker.md#post-status-tracker-assign) | POST | Assign working status to tracker |
| [`/status/tracker/list`](tracker.md#post-status-tracker-list) | POST | List trackers' working statuses |
| [`/status/tracker/read`](tracker.md#post-status-tracker-read) | POST | Read tracker's working status |

#### Listing

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/listing/create`](listing/README.md#post-status-listing-create) | POST | Create working status list |
| [`/status/listing/delete`](listing/README.md#post-status-listing-delete) | POST | Delete working status list |
| [`/status/listing/list`](listing/README.md#post-status-listing-list) | POST | List working status lists |
| [`/status/listing/update`](listing/README.md#post-status-listing-update) | POST | Update working status list |

#### Tracker's working status list

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/listing/tracker/assign`](listing/tracker.md#post-status-listing-tracker-assign) | POST | Assign working status list to tracker |

<!-- endpoint-reference:end -->
