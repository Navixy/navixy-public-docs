---
description: >-
  How IoT Query Premium data is organized: the raw data layer and the
  transformation layer, the IDs that join their tables, and what every
  ClickHouse query needs.
tags:
  - fundamentals
  - preview
---

# Data schema

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

IoT Query Premium organizes its tables in two layers. The two layers use three ClickHouse databases. This page explains what each layer is for, how the tables of different databases join, and the rules that every query needs. It is step 2 of the developer path through [Private Telematics Lakehouse](../), and it is the reference for anyone who writes SQL in Dashboard Studio. Before you start, connect to IoT Query Premium as described in [Connection setup](../connection-setup.md).

## Two layers of data

The raw data layer stores the data as it arrives. The transformation layer contains tables that Navixy calculates from the raw data.

| Layer | Database | What it contains | Use it for |
| --- | --- | --- | --- |
| [Raw data layer](raw-data-layer/) | `core_bdr` | Business data from Business Data Repository | Names, types, groups, and custom fields of your assets, devices, and geo objects |
| [Raw data layer](raw-data-layer/) | `telemetry` | Positions and sensor values from your GPS devices | Every data point and the values that no calculated table has |
| [Transformation layer](transformation-layer/) | `report` | Trips and stops, geo object visits, sensor readings, and device status | Common questions, such as distance per day or time in a geo object |

A typical query reads a table of the transformation layer and joins `core_bdr` to get the names of assets, devices, and geo objects.

## How tables join

The same IDs appear in all three databases. Most queries join tables on four of these IDs:

| ID | Identifies | Where it is defined | Where it is used |
| --- | --- | --- | --- |
| `workspace_id` | A workspace of your organization | `core_bdr.workspace.id` | Almost every table |
| `asset_id` | An asset: a vehicle, a person, or another object that you monitor | `core_bdr.asset.id` | `report` tables |
| `device_id` | A GPS device | `core_bdr.device.id` | `telemetry` and `report` tables |
| `geo_object_id` | A geo object: a zone, a point of interest, or a route | `core_bdr.geo_object.id` | `report` geo object tables, and the `geozone_id` columns of `report.trips_stops` |

To find the asset that a GPS device is installed on, join these business data tables in order:

1. `core_bdr.custom_field_value_device` links a device to its asset: `ref_device_id` is the device, and `entity_id` is the asset.
2. `core_bdr.asset` gives the name, the type, and the workspace of the asset.

The tables of the transformation layer already have `asset_id`, `workspace_id`, and `org_id`, the ID of the organization, which is `organization_id` in `core_bdr.workspace`. You need these two joins only when you start from `telemetry`, which has `device_id` alone.

`core_bdr` stores IDs as the ClickHouse type `UUID`. Most `report` tables store the same IDs as `String`. The exceptions are `device_id` in the sensor tables and `geo_object_id` in the geo object tables, which are `UUID`. Convert a `String` side when you join, for example with `toUUID(t.asset_id)`.

The following query lists the 10 longest trips of the last 7 days with the name of each asset. It doesn't filter out deleted assets, so it also lists the trips of an asset that was deleted since:

```sql
SELECT
    a.title AS asset,
    t.start_time,
    t.end_time,
    round(t.distance_m / 1000, 1) AS distance_km
FROM report.trips_stops AS t
INNER JOIN
(
    SELECT id, title
    FROM core_bdr.asset FINAL
) AS a ON a.id = toUUID(t.asset_id)
WHERE t.kind = 'trip'
  AND t.start_time >= now() - INTERVAL 7 DAY
ORDER BY t.distance_m DESC
LIMIT 10
```

The result has one row per trip:

| asset | start_time | end_time | distance_km |
| --- | --- | --- | --- |
| Truck 12 | 2026-09-24 05:12:40.000 | 2026-09-24 11:48:03.000 | 512.3 |
| Van 3 | 2026-09-22 06:01:15.000 | 2026-09-22 10:20:51.000 | 318.7 |
| Truck 7 | 2026-09-25 13:30:02.000 | 2026-09-25 17:02:44.000 | 287.1 |

## Rules for every query

IoT Query Premium uses ClickHouse SQL. It is close to standard SQL, and the [ClickHouse SQL reference](https://clickhouse.com/docs/reference/home) describes every function. Four rules apply to IoT Query Premium in particular.

### Write each table with its database

Write `core_bdr.asset` or `report.trips_stops`, not `asset` or `trips_stops`. When a table name has no database, ClickHouse looks for the table in the default database of the connection. That database isn't necessarily the database that you mean.

### Read the current version of each row

Several tables keep more than one version of a row. A new version is added each time the source changes, and ClickHouse removes the older versions later, in the background. Until ClickHouse removes the older versions, a plain `SELECT` can return a row twice. Add `FINAL` after the table name to read only the current version:

```sql
SELECT id, title
FROM core_bdr.asset FINAL
```

This rule applies to every `core_bdr` table and to the `report` tables of geo object visits, sensor readings, and current device status. The page of each table says whether it needs `FINAL`. Current geo object status is the exception: read its latest snapshot as described in [report database](transformation-layer/report.md#geo-object-visits).

### Leave out deleted business data

A deleted asset, device, or geo object stays in `core_bdr` with the time of deletion in `deleted_at`. Add `WHERE deleted_at IS NULL` to read only the items that exist. Keep deleted items when you need the names of assets that appear in past trips. Only the main tables have `deleted_at`: [core_bdr database](raw-data-layer/core-bdr.md#how-core_bdr-stores-rows) lists them.

### Convert times to your time zone

Every time column is in UTC, with millisecond precision. Convert a time to a local time zone with `toTimeZone`, for example `toTimeZone(start_time, 'Europe/Madrid')`. Group by local days with `toDate(toTimeZone(start_time, 'Europe/Madrid'))`, because a UTC day starts at a different local hour.

## Tables by layer

* [Raw data layer](raw-data-layer/): Learn what raw data is and when to query it instead of the transformation layer.
  * [core_bdr database](raw-data-layer/core-bdr.md): Find the tables of the workspaces, users, devices, assets, asset groups, geo objects, schedules, catalogs, and custom fields.
  * [telemetry database](raw-data-layer/telemetry.md): Find the columns that store positions and sensor values.
* [Transformation layer](transformation-layer/): Learn which tables Navixy calculates and when to query them.
  * [report database](transformation-layer/report.md): Find the columns of trips, geo object visits, sensor readings, and device status.
