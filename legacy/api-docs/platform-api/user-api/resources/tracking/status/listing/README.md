---
title: Listing
description: The set of working statuses a tracker can be set to, assigned per tracker.
---

# Listing

A working status list is the set of [working statuses](../README.md) a tracker can be set to. Nothing can be assigned to a tracker that is not on the list assigned to that tracker.

Lists are created empty. `status/listing/create` rejects both `id` and `entries`, because a new list holds no statuses yet, and statuses are added afterwards with `status/create`. Once a list holds several, `status/listing/update` reorders them through its `entries` field, which must name the list's full set of status IDs and nothing else.

Working status lists are a tariff feature. An account with no tracker whose plan includes `statuses` gets error 236 from every operation on this page.

## Status listing object structure

{% openapi-schemas spec="navixy-platform" schemas="StatusListing" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/status/listing`.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/listing/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.
* 268 - Over quota, when the user's quota for working status lists is exceeded.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/listing/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no working status list has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.

***

{% openapi-operation spec="navixy-platform" path="/status/listing/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/status/listing/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no working status list has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when no tracker on the account has the `statuses` tariff feature.
* 262 - Entries list is missing some entries or contains nonexistent entries, when `entries` omits a status ID associated with the list, or names one that does not exist.

## More in this section

<!-- endpoint-reference:start -->

#### Tracker's working status list

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/status/listing/tracker/assign`](tracker.md#post-status-listing-tracker-assign) | POST | Assign working status list to tracker |

<!-- endpoint-reference:end -->
