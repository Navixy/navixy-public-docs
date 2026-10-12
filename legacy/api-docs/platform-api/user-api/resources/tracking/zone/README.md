---
title: Geofence
description: Named map areas that limit where a rule applies and that name locations in reports.
---

# Geofence

A geofence is a named area on the map. The API calls it a zone in paths and field names: the path is `/zone`, the object is `Zone`, and a rule binds to one through `zone_ids`.

Geofences limit where a rule applies. A rule that names a geofence reacts only to events inside it. Geofence names also reach reports, printed after the address of an event that happened inside the geofence.

Points belong to polygons and sausages, and a single geofence can carry hundreds of them. The operations that read and write points are on their own page for that reason, [Geofence point](zone_point.md). To create many circle geofences at once from a spreadsheet, use [Geofence import](import.md). For step-by-step instructions on each type, see [how to create each geofence type](../../../guides/places/manage-geofences.md).

## Entity description

A zone object carries a `type` of `circle`, `polygon`, or `sausage`. The type determines which of the remaining fields apply.

{% openapi-schemas spec="navixy-platform" schemas="Zone,ZonePoint" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

#### circle

A centre and a radius. Carries `center` and `radius`, and has no points.

#### polygon

An area bounded by [points](zone_point.md). Carries `points` and no radius.

#### sausage

Represents all points within a certain distance of the given polyline. Carries both `points` and a `radius`, the radius being the distance from the line.

## API actions

API base path: `/zone`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

Preload the file that `file_id` names with [`data/spreadsheet/parse`](../../commons/data.md#post-data-spreadsheet-parse), which converts a spreadsheet to a tab-separated file and returns its name.

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/batch_convert" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 234 - Invalid data format.

***

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 202 - Too many points in a geofence. A polygon allows at most 500 points and a sausage at most 1024.
* 230 - Not supported for this entity type, when `points` were given for a geofence that cannot hold any, such as a circle.
* 268 - Over quota, when the user's quota for geofences is exceeded.
* 284 - Not enough points for the geofence. A polygon needs at least 3 points and a sausage at least 2.

***

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.
* 203 - Delete entity associated with. The response body names what still references the geofence:

```json
{
  "success": false,
  "status": {
    "code": 203,
    "description": "Delete entity associated with"
  },
  "entities": [
    {
      "type": "rules",
      "ids": [12345, 23456]
    }
  ]
}
```

`ids` is an int array holding the IDs of the rules that use the geofence.

***

{% openapi-operation spec="navixy-platform" path="/zone/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/zone/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/zone/search_location" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

A `points` array inside the `zone` object is applied with the rest of the update. [`zone/point/update`](zone_point.md#post-zone-point-update) does the same thing on its own.

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no geofence has the given ID or it belongs to another user.
* 231 - Entity type mismatch, when the submitted geofence's type differs from the type stored in the database.

***

The upload accepts a KML file whose `Placemark` elements define the geofences. A radius given in `ExtendedData` applies to the circles the file produces, and a `Placemark` without one takes the default radius:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
    <Document>
        <name>Points</name>
        <ExtendedData>
            <Data name="radius">
                <value>300</value>
            </Data>
        </ExtendedData>
        <Placemark>
            <name>named point</name>
            <Point>
                <coordinates>44.7463912723,41.7096716534</coordinates>
            </Point>
        </Placemark>
    </Document>
</kml>
```

A `Placemark` name becomes the geofence name, and the `Folder` and `Document` hierarchy around it is preserved.

_Required sub-user rights:_ `zone_update`.

{% openapi-operation spec="navixy-platform" path="/zone/upload" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 202 - Too many points in a geofence. A polygon allows at most 500 points and a sausage at most 1024.
* 233 - No data file, when the `file` part is missing.
* 234 - Invalid data format.
* 268 - Over quota, when the user's quota for geofences is exceeded.
* 284 - Not enough points for the geofence. A polygon needs at least 3 points and a sausage at least 2.

***

The response is a KML or KMZ file holding one `Placemark` per geofence, with the radius of a circle or a sausage carried in `ExtendedData`:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<kml xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom" xmlns:xal="urn:oasis:names:tc:ciq:xsdschema:xAL:2.0">
    <Document>
        <name>zones</name>
        <Placemark>
            <name>polygon_zone</name>
            <visibility>1</visibility>
            <Polygon>
                <outerBoundaryIs>
                    <LinearRing>
                        <coordinates>44.7489290062,41.7201261755 44.7572878562,41.7208046390 44.7562364303,41.7190268459</coordinates>
                    </LinearRing>
                </outerBoundaryIs>
            </Polygon>
        </Placemark>
        <Placemark>
            <name>circle_zone</name>
            <visibility>1</visibility>
            <ExtendedData>
                <Data name="radius">
                    <value>300</value>
                </Data>
            </ExtendedData>
            <Point>
                <coordinates>44.7463912723,41.7096716534</coordinates>
            </Point>
        </Placemark>
        <Placemark>
            <name>sausage_zone</name>
            <visibility>1</visibility>
            <ExtendedData>
                <Data name="radius">
                    <value>300</value>
                </Data>
            </ExtendedData>
            <LineString>
                <coordinates>44.7288827746,41.7176609187 44.7340679137,41.7181063157 44.7384917427,41.7187820845</coordinates>
            </LineString>
        </Placemark>
    </Document>
</kml>
```

{% openapi-operation spec="navixy-platform" path="/zone/download" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

## More in this section

<!-- endpoint-reference:start -->

#### Geofence import

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/zone/import/start`](import.md#post-zone-import-start) | POST | Start geofence import |
| [`/zone/import/read`](import.md#post-zone-import-read) | POST | Read geofence import |
| [`/zone/import/list`](import.md#post-zone-import-list) | POST | List geofence imports |
| [`/zone/import/download_failed`](import.md#post-zone-import-download_failed) | POST | Download failed geofence rows |
| [`/zone/import/finish`](import.md#post-zone-import-finish) | POST | Finish geofence import |

#### Geofence point

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/zone/point/list`](zone_point.md#post-zone-point-list) | POST | List geofence points |
| [`/zone/point/update`](zone_point.md#post-zone-point-update) | POST | Update geofence points |

<!-- endpoint-reference:end -->
