---
title: Places (POI)
description: Business locations that employees visit, called points of interest in the interface and places in the API.
---

# Places (POI)

A place is a location the business cares about and employees visit: a shop, a delivery point, a warehouse.

{% hint style="info" %}
**Places and points of interest are the same thing.** The Navixy interface, the user documentation, and the guides in this section call them points of interest, or POIs. The API calls them places at every level: the path is `/place`, the object is `Place`, and anything referring to one does so through `place_id`.

This page uses "place" throughout, because that is what the API says. Reading POI wherever it appears elsewhere loses nothing.
{% endhint %}

Places do two things beyond sitting on a map. An event that happens inside one is labelled with the place name after the address in reports, and a place can be extended with [custom fields](../../commons/entity/fields.md) so it carries whatever else the business needs.

One custom field type changes what field staff see. When a place carries a responsible-employee field, and that [employee](../employee/README.md) is [assigned](../../tracking/tracker/employee.md#post-tracker-employee-assign) to a device running the Mobile Tracker App, for [Android](https://play.google.com/store/apps/details?id=com.navixy.xgps.tracker\&hl=ru) or [iOS](https://apps.apple.com/us/app/x-gps-tracker/id802887190), the place appears in the app for them. That is how a driver sees the places they are expected to visit.

For a walkthrough, see [how to manage POIs](../../../guides/places/manage-pois.md).

## Place object

{% openapi-schemas spec="navixy-platform" schemas="Place" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

Custom field values are keyed by custom field ID as a string. See [entity/fields](../../commons/entity/fields.md). The avatar fields `icon_id` and `avatar_file_name` are set through [avatar/assign and avatar/upload](avatar.md), not here.

## API actions

API base path: `/place`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/place/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no place has the given ID.

***

The `conditions` parameter takes [search conditions](../../commons/entity/search_conditions.md), and `order_by` takes either a built-in field name or a custom field ID as a string, see [entity](../../commons/entity/README.md).

{% openapi-operation spec="navixy-platform" path="/place/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 268 - Over quota, when the user's quota for places is exceeded.

***

{% openapi-operation spec="navixy-platform" path="/place/search_location" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no place has the given ID.

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no place has the given ID.

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no place has the given ID.

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/batch_convert" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 234 - Invalid data format.

***

_Required sub-user rights:_ `place_update`.

{% openapi-operation spec="navixy-platform" path="/place/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 233 - No data file, when the `file` part is missing.
* 234 - Invalid data format.
* 247 - Entity already exists, when an uploaded place carries an `external_id` that a place already uses and `duplicate_policy` is `fail`.
* 268 - Over quota, when the user's quota for places is exceeded.

