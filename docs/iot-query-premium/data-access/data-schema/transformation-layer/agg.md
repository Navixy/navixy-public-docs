---
description: >-
  The agg database of IoT Query Premium: sensor values and digital inputs and
  outputs per minute, and the positions of devices in geo objects, with their
  columns and example queries.
tags:
  - reference
  - preview
---

# agg database

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

The `agg` database has tables that Navixy calculates from the messages in `telemetry`: sensor values per minute, the state of digital inputs and outputs per minute, and the positions of devices inside geo objects. It belongs to the [transformation layer](./), together with the [report database](report.md). This page describes each table and its columns, and ends with example queries. It is for developers and analysts who build charts of sensor values and reports on geo objects.

## How agg tables are updated

Navixy updates the sensor and digital input tables every 5 minutes, and the geo object table every 15 minutes. Each update calculates the last 14 days again, so a value that a device sends late is added to its minute.

A table can keep more than one version of a row until ClickHouse merges them. Read every `agg` table with `FINAL`, as described in [Data schema](../#read-the-current-version-of-each-row).

Each row has the asset and the device that the value comes from. `asset_id` and `workspace_id` store the `UUID` IDs of `core_bdr` as text: convert them with `toUUID` to join `core_bdr`. `device_id` and `geo_object_id` are already `UUID`. `org_id` contains an empty string. All time columns are in UTC.

## Sensor values

`agg.sensor_readings_5m` groups the values of each signal into 1-minute periods. The table name ends in `_5m`, but each row covers 1 minute. Each row covers one asset, device, signal, and minute. The row has the lowest, highest, first, and last value, the sum of the values, and the number of values. The average of a minute is `val_sum / sample_count`.

The table has the numeric values and the on/off values that a device sends. An on/off value is `1` or `0`. A row belongs to one device, signal, and unit, so an asset with two devices that send the same signal has two rows for each minute. To combine them, add up `val_sum` and `sample_count`.

| Column | Type | What it contains |
| --- | --- | --- |
| `bucket_time` | `DateTime64(3, 'UTC')` | The start of the minute |
| `raw_key` | `String` | The name of the signal, as the GPS device sends it, such as `lls_level_0` |
| `field_unit` | `String` | The unit of the signal, such as `L` |
| `field_type` | `String` | The type of the signal, such as `FLOAT64` or `BOOL` |
| `sensor_type` | `String` | The kind of sensor. Contains `custom` |
| `val_min`, `val_max` | `Float64` | The lowest and the highest value in the minute |
| `val_first`, `val_last` | `Float64` | The first and the last value in the minute |
| `val_sum` | `Float64` | The sum of the values in the minute |
| `sample_count` | `UInt32` | The number of values in the minute |
| `computed_at` | `DateTime64(3, 'UTC')` | When Navixy calculated the row |

## Digital inputs and outputs

`agg.digital_io_5m` has one row for each asset, device, digital input or output, and minute. Like the sensor table, each row covers 1 minute.

| Column | Type | What it contains |
| --- | --- | --- |
| `bucket_time` | `DateTime64(3, 'UTC')` | The start of the minute |
| `io_direction` | `String` | `din` for an input, `dout` for an output |
| `io_index` | `UInt8` | The number of the input or output |
| `state_at_start`, `state_at_end` | `UInt8` | The state at the start and at the end of the minute: `1` for on, `0` for off |
| `change_count` | `UInt16` | How many times the state changed in the minute |
| `on_duration_s` | `UInt16` | How many seconds of the minute the input or output was on |
| `sample_count` | `UInt32` | The number of messages in the minute |
| `computed_at` | `DateTime64(3, 'UTC')` | When Navixy calculated the row |

## Positions in geo objects

`agg.device_geofence_status_hist` shows which geo objects each position of a device is inside. Navixy checks each position against the geo objects of the workspace: polygons, and circles, which are a point with a radius. The table has one row for each position and each geo object that the position is inside. A position outside every geo object has one row with `geo_object_id` `00000000-0000-0000-0000-000000000000`.

The table has positions, not visits. To find how long an asset stayed in a geo object, compare consecutive positions of the same device, as in the example query [Time inside each geo object](#time-inside-each-geo-object).

| Column | Type | What it contains |
| --- | --- | --- |
| `event_time` | `DateTime64(3, 'UTC')` | The time of the position |
| `latitude`, `longitude` | `Float64` | The position |
| `geo_object_id` | `UUID` | The geo object that the position is inside, the same ID as `core_bdr.geo_object.id`. `00000000-0000-0000-0000-000000000000` when the position is outside every geo object |
| `cause` | `String` | Contains `point` |
| `message_id` | `UUID` | Contains `00000000-0000-0000-0000-000000000000` |
| `inserted_at` | `DateTime64(3, 'UTC')` | When Navixy added the row |

## Example queries

### Hourly average of a sensor

The following query returns the hourly average, lowest, and highest fuel level of each asset over the last 24 hours:

```sql
SELECT
    asset_id,
    toStartOfHour(bucket_time) AS hour,
    round(sum(val_sum) / sum(sample_count), 1) AS average_level,
    min(val_min) AS lowest_level,
    max(val_max) AS highest_level
FROM agg.sensor_readings_5m FINAL
WHERE raw_key = 'lls_level_0'
  AND bucket_time >= now() - INTERVAL 1 DAY
GROUP BY asset_id, hour
ORDER BY asset_id, hour
```

The query combines the minutes of every device of an asset that sends the signal. The result has one row per asset and hour:

| asset_id | hour | average_level | lowest_level | highest_level |
| --- | --- | --- | --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 14:00:00.000 | 181.6 | 179.8 | 182.4 |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 15:00:00.000 | 176.2 | 173.9 | 179.7 |

### Time inside each geo object

The following query adds up, for each asset and geo object, the time between consecutive positions inside the geo object over the last 24 hours:

```sql
SELECT
    asset_id,
    geo_object_id,
    round(sum(gap_s) / 60) AS minutes_inside
FROM
(
    SELECT
        asset_id,
        geo_object_id,
        dateDiff('second', event_time,
            leadInFrame(event_time) OVER (
                PARTITION BY device_id, geo_object_id ORDER BY event_time
                ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
            )) AS gap_s
    FROM agg.device_geofence_status_hist FINAL
    WHERE event_time >= now() - INTERVAL 1 DAY
      AND geo_object_id != toUUID('00000000-0000-0000-0000-000000000000')
)
WHERE gap_s > 0 AND gap_s <= 900
GROUP BY asset_id, geo_object_id
ORDER BY minutes_inside DESC
```

The result has one row per asset and geo object:

| asset_id | geo_object_id | minutes_inside |
| --- | --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 8e2f4a1b-0c6d-4d3e-9f7a-5b1c2d3e4f60 | 46 |

The condition `gap_s <= 900` leaves out gaps longer than 15 minutes, when the asset left the geo object or the device sent no positions. For the last position in the period, `leadInFrame` returns no later time, so `dateDiff` gives a negative number and the condition `gap_s > 0` leaves it out.

## See also

* [report database](report.md): Find the trips and stops of your assets.
* [core_bdr database](../raw-data-layer/core-bdr.md): Find the names of the assets, devices, and geo objects that `agg` rows refer to.
* [telemetry database](../raw-data-layer/telemetry.md): Find the messages that the `agg` tables are calculated from.
