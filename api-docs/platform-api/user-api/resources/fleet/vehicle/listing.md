---
title: Vehicle status listing
description: Deprecated. The list of statuses a vehicle could be set to.
---

# Vehicle status listing

{% hint style="warning" %}
**Deprecated.** These actions should not be used.
{% endhint %}

A vehicle status listing was the set of statuses a vehicle could be set to, in the way a [working status list](../../tracking/status/listing/README.md) works for a tracker. The operations still answer, and are documented because they are still reachable, but nothing new should be built on them.

## Vehicle status listing object

{% openapi-schemas spec="navixy-platform" schemas="VehicleStatusListing" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/vehicle/status/listing`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

Reads the vehicle status list of the current user.

***

{% openapi-operation spec="navixy-platform" path="/vehicle/status/listing/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Replaces the vehicle status list. `order` is ignored on update, because statuses already have a position in the array.

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/vehicle/status/listing/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
