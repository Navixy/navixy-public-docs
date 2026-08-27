---
title: Track
description: A tracker's movement over a period, as trips with totals, as raw points, or as a KML file.
---

# Track

A track is what a tracker did over a period: where it went, how far, and how fast. These operations return that movement as trips with totals, as the raw points behind them, or as a KML file for a mapping tool.

What comes back from `track/list` depends on three switches. `split` decides whether the period is broken into separate trips or returned as one `merged` track. `cluster_single_reports` groups one-point trips by coordinate into `cluster` tracks. `filter` drops tracks judged too short by length and point count. The `type` on each track tells you which of those produced it.

These operations take tracker IDs directly. To get the same data for an asset, whose device can change over the period, use [asset tracks](../asset/README.md) instead. For a worked example, see [how to get track points](../../../guides/data-retrieval/get-track-points.md).

## Track object

The object returned in `track/list`'s `list`. Which fields are present depends on `type`.

{% openapi-schemas spec="navixy-platform" schemas="Track" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

`norm_fuel_consumed` is present only when a [vehicle object](../../fleet/vehicle/README.md#vehicle-object) is linked to the tracker and has `normAvgFuelConsumption` defined.

## Track totals object

{% openapi-schemas spec="navixy-platform" schemas="TrackTotals" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Point object

The point object returned by `track/read`, and by `track/list` in each track's `points_list`.

{% openapi-schemas spec="navixy-platform" schemas="TrackPoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

Whether a point counts as trip or as parking is decided by the Navixy platform's [parking detection](https://www.navixy.com/docs/user/web-interface-docs/devices-doc/parking-detection/) settings.

## API actions

API base path: `/track`.

***

The file carries speed and heading as extended data on each placemark:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:atom="http://www.w3.org/2005/Atom" xmlns:xal="urn:oasis:names:tc:ciq:xsdschema:xAL:2.0">
  <Document>
    <name>track-John (Scania) 2023-11-19 03:24:00</name>
    <Placemark>
      <name>point #1</name>
      <visibility>1</visibility>
      <description>2023-11-19 03:24:03</description>
      <TimeStamp>
        <when>2023-11-19T03:24:03.000-06:00</when>
      </TimeStamp>
      <ExtendedData>
        <Data name="speed">
          <value>37</value>
        </Data>
        <Data name="heading">
          <value>27</value>
        </Data>
      </ExtendedData>
      <Point>
        <coordinates>-78.768105,43.1172216</coordinates>
      </Point>
    </Placemark>
  </Document>
</kml>
```

A request reaching further back than the device's plan retains, six months of data on a plan that stores three, returns JSON instead of a file:

```json
{
  "list": [],
  "limit_exceeded": true,
  "success": true
}
```

{% openapi-operation spec="navixy-platform" path="/track/download" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database. The tracker ID may match no tracker on the account the session hash belongs to. Check that the `tracker_id` and the hash belong to the same user.
* 208 - Device blocked, when the tracker exists on the account but is inactive, whether through tariff restrictions or for another reason.
* 211 - Requested time span is too big, when the interval between `from` and `to` exceeds the maximum the API configuration allows.

***

{% openapi-operation spec="navixy-platform" path="/track/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database. The tracker ID may match no tracker on the account the session hash belongs to. Check that the `tracker_id` and the hash belong to the same user.
* 208 - Device blocked, when the tracker exists on the account but is inactive, whether through tariff restrictions or for another reason.
* 211 - Requested time span is too big, when the interval between `from` and `to` exceeds the maximum the API configuration allows.

***

{% openapi-operation spec="navixy-platform" path="/track/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database. The tracker ID may match no tracker on the account the session hash belongs to. Check that the `tracker_id` and the hash belong to the same user.
* 208 - Device blocked, when the tracker exists on the account but is inactive, whether through tariff restrictions or for another reason.
* 211 - Requested time span is too big, when the interval between `from` and `to` exceeds the maximum the API configuration allows.

## Visits

`track/visit/list` answers a different question from the operations above: not where the tracker went, but which of the account's geofences and places it was inside during the period.

***

{% openapi-operation spec="navixy-platform" path="/track/visit/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - Entity not found. The tracker ID may match no tracker on the account the session hash belongs to. Check that the `tracker_id` and the hash belong to the same user.

## More in this section

<!-- endpoint-reference:start -->

#### Waybill

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/track/waybill/download`](waybill/README.md#post-track-waybill-download) | POST | Download waybill |

#### Waybill settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/track/waybill/settings/read`](waybill/settings.md#post-track-waybill-settings-read) | POST | Read last waybill number |

<!-- endpoint-reference:end -->
