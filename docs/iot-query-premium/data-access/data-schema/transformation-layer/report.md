---
description: >-
  The report database of IoT Query Premium: the trips and stops of your assets,
  how Navixy detects them, and the columns of report.trips_stops.
tags:
  - reference
  - preview
---

# report database

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

The `report` database has the trips and stops of your assets, which Navixy calculates from telemetry and business data. It belongs to the [transformation layer](./), together with the [agg database](agg.md). This page describes how Navixy detects trips and stops and lists the columns of `report.trips_stops`. It ends with an example query. It is for developers and analysts who build reports and dashboards on trips.

## Trips and stops

`report.trips_stops` has one row for each trip and one row for each stop of an asset. `kind` has the value `trip` or `stop`.

### How trips and stops are detected

Navixy reads the positions of a device in time order and splits them into moving and standing periods:

* A position counts as moving when the speed is more than 3 km/h. `detection_method` is `speed` for a trip and empty for a stop.
* A standing period of at least 5 minutes is a stop. A shorter standing period stays part of the trip and counts as its parked time.
* A gap of more than 15 minutes between two positions ends the current trip or stop.
* A trip shorter than 50 m isn't recorded as a trip.

Trip counts in IoT Query Premium can differ slightly from [Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/reports) in Location Intelligence for the same asset.

If the device sends an odometer, the distance of a trip is the difference between the odometer values at the start and at the end of the trip. Otherwise, it is the sum of the straight-line distances between consecutive positions.

Navixy calculates trips and stops every 4 hours, from the positions of the last 14 days. A trip or a stop is added to `report.trips_stops` after it ends: when the next trip or stop has started, or 24 hours after it started. A row doesn't change after it is added, so a trip in progress isn't in the table. The `id` of a row is calculated from the asset, `kind`, and the start time, so you can store it in your own systems.

`report.trips_stops` doesn't need `FINAL`: Navixy adds each trip or stop once.

### Columns of report.trips_stops

| Column | Type | What it contains |
| --- | --- | --- |
| `workspace_id` | `String` | The workspace of the asset |
| `asset_id` | `String` | The asset |
| `device_id` | `String` | The GPS device that the positions come from |
| `kind` | `String` | `trip` or `stop` |
| `id` | `String` | The ID of the trip or stop |
| `detection_method` | `String` | `speed` for a trip, empty for a stop |
| `date` | `Date` | The UTC date of `start_time` |
| `start_time`, `end_time` | `DateTime64(3, 'UTC')` | When the trip or stop started and ended |
| `duration_s` | `UInt32` | The duration, in seconds |
| `moving_s`, `parked_s` | `Nullable(UInt32)` | For a trip, the seconds spent moving and the seconds spent standing for less than 5 minutes |
| `distance_m` | `Nullable(UInt32)` | The distance of a trip, in meters |
| `avg_speed_kmh`, `max_speed_kmh` | `Nullable(Float32)` | The average and the highest speed of a trip, in km/h |
| `start_lat`, `start_lon` | `Nullable(Float64)` | Where a trip started |
| `end_lat`, `end_lon` | `Nullable(Float64)` | Where a trip ended |
| `latitude`, `longitude` | `Nullable(Float64)` | Where a stop took place |
| `points_count` | `Nullable(UInt32)` | The number of positions that the row was calculated from |
| `inserted_at` | `DateTime64(3, 'UTC')` | When Navixy added the row |
| `org_id` | `String` | Contains an empty string |
| `asset_group_id`, `driver_id`, `shift_id`, `schedule_id`, `start_geozone_id`, `end_geozone_id`, `geozone_id`, `prev_id`, `next_id`, `stop_after_s` | `Nullable` | These columns contain `NULL` |

`workspace_id`, `asset_id`, and `device_id` store the `UUID` IDs of `core_bdr` as text. To join them, convert them with `toUUID`, as shown in [Data schema](../#how-tables-join). All time columns are in UTC.

## Example query

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

## See also

* [agg database](agg.md): Find the sensor values, the digital inputs and outputs, and the positions in geo objects.
* [core_bdr database](../raw-data-layer/core-bdr.md): Find the names of the assets and devices that `report` rows refer to.
* [telemetry database](../raw-data-layer/telemetry.md): Find the messages that trips and stops are calculated from.
