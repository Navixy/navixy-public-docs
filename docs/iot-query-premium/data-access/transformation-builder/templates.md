---
hidden: true
noIndex: true
description: >-
  Ready-made Transformation Builder workflows that you can start from: trips
  with your own detection rules and sensor values in periods of your choice.
tags:
  - reference
  - preview
---

# Templates

A template is a ready-made workflow file for a common transformation, such as trips. You import it into Transformation Builder, change its rules where you need to, and schedule it to build a table of your own. This page lists the templates and what each template produces. It also names the `report` table with the same kind of data and gives the settings for the Output node and the schedule. It is for developers and analysts who need a rule that the `report` tables don't apply. Before you start, read [Transformation Builder](./) to learn how workflows and nodes work.

## Templates and their report tables

Each template builds a table of the same kind as a table that Navixy already calculates in the [report database](../data-schema/transformation-layer/report.md):

| Template | What it produces | Table in `report` |
| --- | --- | --- |
| [Trips](#trips) | One row per trip, with the geo objects where the trip started and ended | `report.trips_stops`, the rows with `kind` `trip` |
| [Trips without geo objects](#trips-without-geo-objects) | One row per trip | `report.trips_stops`, the rows with `kind` `trip` |
| [Sensor values per period](#sensor-values-per-period) | The values of each sensor in periods of a length that you choose | `report.sensor_readings` and `report.sensor_readings_rollup` |

Query the `report` table first. It is ready without any setup, and a filter or a grouping in your query often answers the question. Use a template when you need a different rule, such as a different minimum length of a trip.

A template writes to a table of your own. It doesn't change the `report` tables.

## How a template is used

A template is a workflow file that you import into Transformation Builder with **Import**. After the import, you change the rules that you need. Then you check the first 100 rows of the result with **Preview** and set when the workflow runs with **Schedule**. The section of each template lists the rules that you can change, the settings of its Output node, and a schedule that fits it. [Workflow YAML reference](workflow-yaml-reference.md) describes the keys of each node.

## Trips

The Trips template produces one row for each trip of each device. It calculates the trips from the positions in `telemetry`. Each row has the start and end of the trip, its duration, its distance, its speeds, and the geo objects where the trip started and ended. The geo objects come from business data.

### How the trips template detects trips

The template reads the positions of each device in time order and applies these rules:

* A position counts as moving when its speed is at least 3 km/h. The device must also have moved more than 50 m since the previous position, or report that it is moving.
* A position is left out when its distance from the previous position means a speed above 200 km/h.
* A stop of at least 5 minutes ends a trip.
* A gap of 20 minutes or more between two messages starts a new trip.
* A trip is kept when it has at least 2 positions, a highest speed of at least 3 km/h, and a length of at least 100 m.
* The distance of a trip is the length of the line through its positions.
* Each run reads the positions of the last 12 hours.

Each rule is a value in the SQL of a node of the template. Change a value there to apply your own rule. To compare the rules with the trips that Navixy calculates, see [How trips and stops are detected](../data-schema/transformation-layer/report.md#how-trips-and-stops-are-detected).

### Columns of the trips result

Most columns have a counterpart in `report.trips_stops`:

| Column | What it contains | Column of `report.trips_stops` |
| --- | --- | --- |
| `device_id` | The GPS device | `device_id` |
| `trip_start_time`, `trip_end_time` | When the trip started and ended, in UTC | `start_time`, `end_time` |
| `trip_duration_seconds` | The duration, in seconds | `duration_s` |
| `trip_duration` | The duration as text, such as `01:42:10` | None |
| `trip_distance_meters` | The distance, in meters | `distance_m` |
| `avg_speed`, `max_speed`, `min_speed` | The average, highest, and lowest speed, in km/h | `avg_speed_kmh`, `max_speed_kmh` |
| `latitude_start`, `longitude_start`, `altitude_start` | Where the trip started | `start_latitude`, `start_longitude` |
| `latitude_end`, `longitude_end`, `altitude_end` | Where the trip ended | `end_latitude`, `end_longitude` |
| `points_in_trip` | The number of positions in the trip | `points_count` |
| `start_zone`, `end_zone` | The name of the geo object where the trip started and ended, if any | `start_geozone_id`, `end_geozone_id`, which contain the ID of the geo object |

The result identifies the device by `device_id` and has no `asset_id`. To group trips by asset, join `core_bdr.device_relation`, as described in [Data schema](../data-schema/#how-tables-join).

### Output node and schedule for trips

The Trips template has no Output node. Add one with these settings:

| Key | Value |
| --- | --- |
| `table_name` | `trips` |
| `time_column` | `trip_start_time` |
| `primary_key` | `device_id`, `trip_start_time` |
| `write_mode` | `upsert` |
| `partition_by` | `toDate(trip_start_time)` |

Schedule the workflow with `10 */8 * * *`, which runs at 10 minutes past 00:00, 08:00, and 16:00 UTC. Each run reads the last 12 hours, so each run reads the last 4 hours of the previous run again. `upsert` updates a trip that an earlier run already wrote, instead of adding it twice.

## Trips without geo objects

The Trips without geo objects template produces the same rows as [Trips](#trips), without `start_zone` and `end_zone`. It doesn't check the positions against geo objects, so it reads less data and runs faster. Use it when you don't need to know where trips started and ended.

The rules, the other columns, the Output node settings, and the schedule are the same as for Trips. See [How the trips template detects trips](#how-the-trips-template-detects-trips) and [Output node and schedule for trips](#output-node-and-schedule-for-trips).

## Sensor values per period

The Sensor values per period template produces the values of each sensor grouped into periods of a length that you choose. The length of the period is written in the SQL of its Custom node. Change it there.

Use the template when you need a period that the `report` tables don't have. `report.sensor_readings` has periods of 1 minute. `report.sensor_readings_rollup` has periods of 10 minutes, 30 minutes, 1 hour, 4 hours, and 1 day.

A query can often build the period that you need from the 1-minute periods, without a workflow. The following query returns the fuel level of each asset in 5-minute periods over the last day:

```sql
SELECT
    asset_id,
    toStartOfInterval(bucket_time, INTERVAL 5 MINUTE) AS period_start,
    min(val_min) AS lowest,
    max(val_max) AS highest,
    round(sum(val_sum) / sum(sample_count), 1) AS average
FROM report.sensor_readings FINAL
WHERE raw_key = 'lls_level_0'
  AND bucket_time >= now() - INTERVAL 1 DAY
GROUP BY asset_id, period_start
ORDER BY asset_id, period_start
```

The result has one row per asset and 5-minute period:

| asset_id | period_start | lowest | highest | average |
| --- | --- | --- | --- | --- |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 14:00:00.000 | 181.9 | 182.4 | 182.2 |
| 1c7a0e52-3b9d-4f18-a6e2-7d40c95b1f83 | 2026-09-27 14:05:00.000 | 181.2 | 181.9 | 181.5 |

Build the table with the template when many dashboards read the same periods. Each dashboard then reads a small table instead of grouping the 1-minute periods again.

### Output node and schedule for sensor values

The Sensor values per period template has an Output node. Change these settings in it:

| Key | Value |
| --- | --- |
| `table_name` | A name of your own that includes the period, such as `sensor_values_5min` |
| `write_mode` | `append` |

Schedule the workflow to run once per period, so that each run adds one period to the table. For 5-minute periods, use `*/5 * * * *`. The SQL of the Custom node must read exactly one period per run, the same length as the schedule. If a run reads more than one period, `append` adds the same rows again at each run. If you change the period, change the SQL and the schedule together.

## See also

* [Transformation Builder](./): Learn what a workflow is, what each node does, and where the result is stored.
* [Workflow YAML reference](workflow-yaml-reference.md): Look up the keys of the template files and of the nodes that you change.
* [report database](../data-schema/transformation-layer/report.md): Find the trips and sensor periods that Navixy calculates, and the rules behind them.
