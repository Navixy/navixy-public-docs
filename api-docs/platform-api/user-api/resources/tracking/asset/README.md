---
title: Asset
description: Tracks for an asset, resolved across every tracker the asset was attached to over the period.
---

# Asset

An asset is tracked through whichever device is attached to it at the time, and that device can change. These operations take an asset and resolve its trackers, which is what separates them from `track/*`, where the caller supplies tracker IDs directly.

Each entry in a response carries its own `interval`: the window during which the asset was attached to that tracker, not the window the request asked for. An asset moved between devices mid-period therefore comes back as several entries.

A track arrives as one of four kinds, selected by its `type`. `regular` is an ordinary trip. `single_report` covers a device reporting in interval mode, or a period holding only one point. `merged` is the whole period as one track, returned when `split` was `false`. `cluster` groups one-point trips by coordinate, returned when `cluster_single_reports` was `true`.

## Point object

The point object both operations return, in `points_list` on a track and in `track_points` on a read:

{% openapi-schemas spec="navixy-platform" schemas="TrackPoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

Whether a point counts as trip or as parking is decided by the Navixy platform's [parking detection](https://www.navixy.com/docs/user/web-interface-docs/devices-doc/parking-detection/) settings.

## API actions

API base path: `/asset/track`.

***

{% openapi-operation spec="navixy-platform" path="/asset/track/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no asset has the given ID.
* 211 - Requested time span is too big, when the interval between `from` and `to` exceeds the maximum the API configuration allows.

***

The timestamp on each point is the moment the tracker recorded it, adjusted to the user's time zone.

{% openapi-operation spec="navixy-platform" path="/asset/track/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no asset has the given ID.
* 211 - Requested time span is too big, when the interval between `from` and `to` exceeds the maximum the API configuration allows.

## More in this section

<!-- endpoint-reference:start -->

#### Asset groups

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/asset_group/create`](asset-groups.md#post-asset_group-create) | POST | Create asset group |
| [`/asset_group/list`](asset-groups.md#post-asset_group-list) | POST | List asset groups |
| [`/asset_group/set`](asset-groups.md#post-asset_group-set) | POST | Set assets in group |
| [`/asset_group/remove`](asset-groups.md#post-asset_group-remove) | POST | Remove assets from group |
| [`/asset_group/update`](asset-groups.md#post-asset_group-update) | POST | Rename asset group |
| [`/asset_group/delete`](asset-groups.md#post-asset_group-delete) | POST | Delete asset group |

<!-- endpoint-reference:end -->
