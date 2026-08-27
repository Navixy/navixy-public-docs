---
title: Geofence point
description: The points that give a polygon or sausage geofence its shape, read and replaced separately from the geofence.
---

# Geofence point

The points of a geofence define its shape. A polygon is bounded by its points, and a sausage runs along them. A circle has none and rejects any that are sent.

Points are handled separately from the geofence itself because one geofence can carry hundreds of them, which is why [`zone/list`](README.md#post-zone-list) and [`zone/read`](README.md#post-zone-read) leave them out unless `with_points` is set. The two operations here read and replace them directly.

## Point object structure

{% openapi-schemas spec="navixy-platform" schemas="ZonePoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/zone/point`.

***

{% openapi-operation spec="navixy-platform" path="/zone/point/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no geofence has the given ID or it belongs to another user.
* 230 - Not supported for this entity type, when the geofence cannot hold points at all, such as a circle.

***

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/point/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no geofence has the given ID or it belongs to another user.
* 202 - Too many points in a geofence, when the `points` array exceeds the limit for the geofence's type. A polygon allows at most 500 points and a sausage at most 1024.
* 230 - Not supported for this entity type, when the geofence cannot hold points at all, such as a circle.
* 284 - Not enough points for the geofence. A polygon needs at least 3 points and a sausage at least 2.
