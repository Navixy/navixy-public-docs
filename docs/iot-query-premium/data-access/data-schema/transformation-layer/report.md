---
description: >-
  The calculated tables of the report database in IoT Query Premium: trips and
  stops, geo object visits, sensor readings, and device status, with how Navixy
  calculates each one.
tags:
  - reference
  - preview
---

# report database

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

The `report` database has the tables that Navixy calculates from telemetry and business data. The tables cover trips and stops, entries to and exits from geo objects, sensor values per period, and the state of each device. It belongs to the [transformation layer](./). This page describes each table: what one row is, how Navixy calculates it, and its columns. It ends with example queries. It is for developers and analysts who build reports and dashboards on calculated data.

## Columns that every table has

Every `report` table has the following ID columns. They tell you which organization, workspace, asset, and device a row belongs to:

| Column | Type | What it contains |
| --- | --- | --- |
| `org_id` | `String` | The organization |
| `workspace_id` | `String` | The workspace of the asset |
| `asset_id` | `String` | The asset that the device is installed on. In the device status tables, an empty string when the device has no asset |
| `device_id` | `String`, or `UUID` in the sensor tables | The GPS device |

These columns store the `UUID` IDs of `core_bdr` as text. To join them, convert them with `toUUID`, as shown in [Data schema](../#how-tables-join). In the sensor tables, `device_id` is already a `UUID` and needs no conversion. All time columns are in UTC.

## Trips and stops

`report.trips_stops` has one row for each trip and one row for each stop of an asset. `kind` has the value `trip` or `stop`.

### How trips and stops are detected

Navixy reads the positions of a device in time order and splits them into moving and standing periods:

* A position counts as standing when the speed is 3 km/h or less. A device can also be set up to detect trips by ignition instead: then a trip lasts while the ignition is on. `detection_method` has the rule that was used for the row: `speed` or `ignition`.
* A standing period of at least 5 minutes is a stop. A shorter standing period stays part of the trip and counts as its parked time.
* A gap of more than 20 minutes between two messages ends the current trip or stop.
* A trip shorter than 50 m isn't recorded as a trip.

Trip counts in IoT Query Premium can differ slightly from [Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/reports) in Location Intelligence for the same asset.

If the device sends an odometer, the distance of a trip is the difference between the odometer values at the start and at the end of the trip. Otherwise, it is the sum of the straight-line distances between consecutive positions.

Navixy calculates the trips and stops of the last 12 hours again at each run, so a trip in progress can change until it ends. The `id` of a completed trip or stop stays the same across runs. The `id` of a trip or stop in progress changes until it ends, because the `id` is calculated from the start and the end time.

### Columns of report.trips_stops

| Column | Type | What it contains |
| --- | --- | --- |
| `kind` | `String` | `trip` or `stop` |
| `id` | `String` | The ID of the trip or stop. For a completed trip or stop, it stays the same when the table is recalculated |
| `detection_method` | `String` | The rule that detected the row: `speed` or `ignition` |
| `date` | `Date` | The UTC date of `start_time` |
| `start_time`, `end_time` | `DateTime64(3, 'UTC')` | When the trip or stop started and ended |
| `duration_s` | `UInt32` | The duration, in seconds |
| `moving_s`, `parked_s` | `Nullable(UInt32)` | For a trip, the seconds spent moving and the seconds spent standing for less than 5 minutes |
| `distance_m` | `Nullable(UInt32)` | The distance of a trip, in meters |
| `avg_speed_kmh`, `max_speed_kmh` | `Nullable(Float32)` | The average and the highest speed of a trip, in km/h |
| `start_latitude`, `start_longitude` | `Nullable(Float64)` | Where a trip started |
| `end_latitude`, `end_longitude` | `Nullable(Float64)` | Where a trip ended |
| `latitude`, `longitude` | `Nullable(Float64)` | Where a stop took place |
| `start_geozone_id`, `end_geozone_id` | `Nullable(String)` | The geo object that a trip started or ended in, if any. One geo object, even when the position is inside several |
| `geozone_id` | `Nullable(String)` | The geo object that a stop took place in, if any. One geo object, even when the position is inside several |
| `prev_id`, `next_id` | `Nullable(String)` | The `id` of the previous and the next row of the same asset. Follow them to read a day as a chain of trips and stops |
| `stop_after_s` | `Nullable(UInt32)` | The duration of the stop after a trip, in seconds |
| `points_count` | `Nullable(UInt32)` | The number of positions that the row was calculated from |
| `asset_group_id` | `Nullable(String)` | The group of the asset |
| `driver_id`, `shift_id`, `schedule_id` | `Nullable(String)` | These columns contain `NULL` |

`report.trips_stops` doesn't need `FINAL`: each recalculation replaces the rows of the period that it covers.

## Geo object visits

A geo object visit is the time that an asset spends inside a geo object: a zone or a point of interest from Business Data Repository. Navixy checks each position of a device against the shapes of the geo objects in its workspace. When a position is inside a geo object and the previous position isn't, the asset enters the geo object. When a position is outside and the previous position is inside, the asset leaves the geo object.

An asset can be inside several geo objects at the same time, for example a depot inside a city zone. Each geo object has its own visit.

An exit is recorded when the device sends a position from outside the geo object. If the device stops sending messages inside a geo object, the visit stays open until the next message.

### Columns of report.device_geofence_status_hist

`report.device_geofence_status_hist` has one row for each entry and one row for each exit. Read it with `FINAL`, which returns the current version of each row: see [Data schema](../#read-the-current-version-of-each-row).

| Column | Type | What it contains |
| --- | --- | --- |
| `geo_object_id` | `UUID` | The geo object, the same ID as `core_bdr.geo_object.id` |
| `kind` | `String` | `enter` or `exit` |
| `event_time` | `DateTime64(3, 'UTC')` | When the asset entered or left |
| `session_id` | `UInt64` | The number of the visit. The entry and the exit of one visit have the same `session_id` for the same device and geo object |

A visit with an `enter` row and no `exit` row is still in progress.

### Columns of report.device_geofence_status

`report.device_geofence_status` has the geo objects that each device is in now. Navixy adds a new snapshot at each refresh, and a geo object that the device has left isn't in the new snapshot. To read the current geo objects, read the latest snapshot:

```sql
SELECT device_id, geo_object_id, event_time
FROM report.device_geofence_status
WHERE inserted_at = (SELECT max(inserted_at) FROM report.device_geofence_status)
```

Don't use `FINAL` on this table: it also returns the geo objects that the device has left.

| Column | Type | What it contains |
| --- | --- | --- |
| `geo_object_id` | `UUID` | The geo object that the device is in |
| `event_time` | `DateTime64(3, 'UTC')` | The time of the latest position of the device, which is inside the geo object |
| `inserted_at` | `DateTime64(3, 'UTC')` | The time of the snapshot that the row belongs to |
| `latitude`, `longitude` | `Nullable(Float64)` | That position |

## Sensor readings

Navixy groups the values of each sensor into periods. Each row covers one asset, device, signal, and period. The row has the lowest, highest, first, and last value, the sum of the values, and the number of values. The average of a period is `val_sum / sample_count`.

`report.sensor_readings` has 1-minute periods. `report.sensor_readings_rollup` has longer periods, with the length in `interval_sec`:

| `interval_sec` | Period |
| --- | --- |
| `600` | 10 minutes |
| `1800` | 30 minutes |
| `3600` | 1 hour |
| `14400` | 4 hours |
| `86400` | 1 day |

Read both tables with `FINAL`. A row belongs to one device, signal, and unit, so an asset with two devices that send the same signal has two rows for each period. To combine them, add up `val_sum` and `sample_count`. Choose the longest period that your chart needs. A chart of one month with one value per day reads 30 rows per signal from the 1-day periods, instead of 43,200 rows from the 1-minute periods.

### Columns of the sensor tables

| Column | Type | What it contains |
| --- | --- | --- |
| `bucket_time` | `DateTime64(3, 'UTC')` | The start of the period |
| `interval_sec` | `UInt32` | The length of the period, in seconds. Only in `report.sensor_readings_rollup` |
| `raw_key` | `String` | The name of the signal, as the GPS device sends it, such as `lls_level_0` |
| `field_unit` | `String` | The unit of the signal, as the device reports it |
| `field_type` | `String` | The type of the signal |
| `sensor_type` | `String` | The kind of sensor. Contains `custom` |
| `mapping_id` | `Nullable(String)` | Contains `NULL`. Only in `report.sensor_readings` |
| `val_min`, `val_max` | `Float64` | The lowest and the highest value in the period |
| `val_first`, `val_last` | `Float64` | The first and the last value in the period |
| `val_sum` | `Float64` | The sum of the values in the period |
| `sample_count` | `UInt32` | The number of values in the period |

## Device status

For each position, device status shows whether the device is connected and whether the device moves. `report.device_status_hist` has one row per position. `report.device_status` has one row per device, with its latest state. Read `report.device_status` with `FINAL`.

A state older than 10 minutes isn't written to `report.device_status`, so a late message doesn't replace a newer state. `report.device_status_hist` keeps late messages, so that you can read the state at any past time.

### Columns of the device status tables

| Column | Type | What it contains |
| --- | --- | --- |
| `event_time` | `DateTime64(3, 'UTC')` | The time of the state |
| `fix_time` | `Nullable(DateTime64(3, 'UTC'))` | The time of the position fix |
| `connectivity` | `String` | `online`, `offline`, `standby`, or `unknown` |
| `motion` | `String` | `moving`, `stopped`, `parked`, or `unknown` |
| `cause` | `String` | `telemetry` when a message caused the state, `timer` when the device stayed silent too long |
| `ignition` | `Nullable(Bool)` | Whether the ignition is on |
| `speed` | `Nullable(Float32)` | Speed, in km/h |
| `heading` | `Nullable(Float32)` | Direction of travel, in degrees |
| `latitude`, `longitude` | `Nullable(Float64)` | The position |
| `altitude` | `Nullable(Int32)` | Altitude, in meters |
| `fix_type` | `LowCardinality(Nullable(String))` | The type of position fix |
| `hdop`, `pdop` | `Nullable(Float32)` | How precise the position fix is. A lower value is more precise |
| `message_id` | `UUID` | The message that the state comes from |

The position columns contain `NULL` when the message that caused the state has no usable position. A state with `cause` `timer` doesn't come from the device. Navixy adds this state when a device sends no messages for longer than a set time, to change `connectivity` to `offline` or `standby`.

## Example queries

### Distance and driving time per asset and day

The following query sums the trips of each asset for each of the last 7 days:

```sql
SELECT
    asset_id,
    date,
    count() AS trips,
    round(sum(distance_m) / 1000, 1) AS distance_km,
    round(sum(moving_s) / 3600, 1) AS moving_hours
FROM report.trips_stops
WHERE kind = 'trip'
  AND date > today() - 7
GROUP BY asset_id, date
ORDER BY asset_id, date
```

The result has one row per asset and day:

| asset_id | date | trips | distance_km | moving_hours |
| --- | --- | --- | --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-26 | 6 | 212.4 | 4.1 |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 | 4 | 158.9 | 3.2 |

`date` is a UTC date. For local days, group by `toDate(toTimeZone(start_time, 'Europe/Madrid'))` instead.

### Time spent in each geo object

The following query pairs the entry and exit of each visit in the last 24 hours and calculates its duration:

```sql
SELECT
    asset_id,
    geo_object_id,
    session_id,
    minIf(event_time, kind = 'enter') AS entered_at,
    maxIf(event_time, kind = 'exit') AS left_at,
    dateDiff('minute', entered_at, left_at) AS minutes_inside
FROM report.device_geofence_status_hist FINAL
WHERE event_time >= now() - INTERVAL 1 DAY
GROUP BY asset_id, device_id, geo_object_id, session_id
HAVING countIf(kind = 'exit') > 0
ORDER BY entered_at
```

The result has one row per completed visit:

| asset_id | geo_object_id | session_id | entered_at | left_at | minutes_inside |
| --- | --- | --- | --- | --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 8e2f4a1b-0c6d-4d3e-9f7a-5b1c2d3e4f60 | 14 | 2026-09-27 07:02:11.000 | 2026-09-27 07:48:35.000 | 46 |

`HAVING countIf(kind = 'exit') > 0` leaves out visits that are still in progress. A visit that started more than a day ago has its entry outside the period. Its `entered_at` is then `1970-01-01 00:00:00.000`, the value that `minIf` returns when no row matches. Widen the period to include the entry.

### Hourly average of a sensor

The following query returns the hourly average, lowest, and highest fuel level of each asset over the last 24 hours:

```sql
SELECT
    asset_id,
    bucket_time,
    round(sum(val_sum) / sum(sample_count), 1) AS average_level,
    min(val_min) AS lowest_level,
    max(val_max) AS highest_level
FROM report.sensor_readings_rollup FINAL
WHERE interval_sec = 3600
  AND raw_key = 'lls_level_0'
  AND bucket_time >= now() - INTERVAL 1 DAY
GROUP BY asset_id, bucket_time
ORDER BY asset_id, bucket_time
```

The query combines the rows of every device of an asset that sends the signal. The result has one row per asset and hour:

| asset_id | bucket_time | average_level | lowest_level | highest_level |
| --- | --- | --- | --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 14:00:00.000 | 181.6 | 179.8 | 182.4 |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 15:00:00.000 | 176.2 | 173.9 | 179.7 |

## See also

* [core_bdr database](../raw-data-layer/core-bdr.md): Find the names of the assets, devices, and geo objects that `report` rows refer to.
* [telemetry database](../raw-data-layer/telemetry.md): Find the messages that the `report` tables are calculated from.
