---
title: Geo links
description: Shareable sessions that show the live location of chosen trackers to someone without an account.
---

# Geo links

A geo link is a shareable session that shows the live location of chosen trackers to someone who has no account on the Navixy platform. It is how a courier's position is shown to the customer expecting the delivery, or a vehicle's to a client waiting for it.

A link is **active the moment it is created**, so treat the hash the create call returns as live from that instant. `status/change` turns a link off without deleting it, and doing so also closes any session currently open against it.

Geo links replace the older Weblocator feature, which is why the right that governs them is still called `weblocator_session_create` and the tariff feature is still `weblocator`.

## Geo link object

{% openapi-schemas spec="navixy-platform" schemas="LocationLink" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Lifetime object

{% openapi-schemas spec="navixy-platform" schemas="LocationLinkLifetime" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Tracker object

{% openapi-schemas spec="navixy-platform" schemas="LocationLinkTracker" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Params object

{% openapi-schemas spec="navixy-platform" schemas="LocationLinkParams" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Display options object

{% openapi-schemas spec="navixy-platform" schemas="LocationLinkDisplayOptions" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/tracker/location/link`.

Operations that list no errors of their own return only the [general error codes](../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `weblocator_session_create`.

{% openapi-operation spec="navixy-platform" path="/tracker/location/link/create" method="post" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../general/errors.md#error-codes):

* 13 - Operation not permitted, when the user has insufficient rights.
* 204 - Entity not found, when a geofence or place in the request is not found.
* 217 - List contains nonexistent entities, when a tracker ID belongs to no tracker or to another user's.
* 236 - Feature unavailable due to tariff restrictions, when a tracker in the request has no `weblocator` tariff feature.
* 268 - Link cannot be created due to quota violation.

***

_Required sub-user rights:_ `weblocator_session_create`.

{% openapi-operation spec="navixy-platform" path="/tracker/location/link/update" method="post" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../general/errors.md#error-codes):

* 13 - Operation not permitted, when the user has insufficient rights.
* 201 - Not found in the database, when no link has the given ID or it belongs to another user.
* 204 - Entity not found, when a geofence or place in the request is not found.
* 217 - List contains nonexistent entities, when a tracker ID belongs to no tracker or to another user's.
* 236 - Feature unavailable due to tariff restrictions, when a tracker in the request has no `weblocator` tariff feature.

***

_Required sub-user rights:_ `weblocator_session_create`.

{% openapi-operation spec="navixy-platform" path="/tracker/location/link/status/change" method="post" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../general/errors.md#error-codes):

* 201 - Not found in the database, when no link has the given ID or it belongs to another user.

***

{% openapi-operation spec="navixy-platform" path="/tracker/location/link/read" method="post" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../general/errors.md#error-codes):

* 201 - Not found in the database, when no link has the given ID or it belongs to another user.

***

`filter` and `conditions` may be combined, in which case a link must match both. `conditions` takes the same [search conditions](commons/entity/search_conditions.md) used elsewhere in the API.

{% openapi-operation spec="navixy-platform" path="/tracker/location/link/list" method="post" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/tracker/location/link/delete" method="post" %}
[OpenAPI navixy-platform](../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../general/errors.md#error-codes):

* 201 - Not found in the database, when no link has the given ID or it belongs to another user.
