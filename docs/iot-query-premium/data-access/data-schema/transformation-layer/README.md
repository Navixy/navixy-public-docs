---
description: >-
  The transformation layer of IoT Query Premium: tables that Navixy calculates
  from raw data, such as trips and sensor values per minute, and when to query
  them.
tags:
  - fundamentals
  - preview
---

# Transformation layer

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Navixy calculates the tables of the transformation layer for you from the [raw data layer](../raw-data-layer/). The tables cover trips and stops, sensor values and digital inputs per minute, and the positions of devices in geo objects. This page lists the tables and explains when to query them instead of the raw data. It is for developers and analysts who choose which tables to query.

## Common transformations

Navixy calculates a set of tables for every workspace, in the `report` and `agg` databases. You don't configure them: they are ready as soon as your GPS devices send data.

| Table | One row is | Calculated from | Updated |
| --- | --- | --- | --- |
| `report.trips_stops` | A trip or a stop of an asset | Positions and speed in `telemetry` | Every 4 hours |
| `agg.sensor_readings_5m` | The values of one signal over 1 minute | Sensor values in `telemetry` | Every 5 minutes |
| `agg.digital_io_5m` | The state of one digital input or output over 1 minute | Digital inputs and outputs in `telemetry` | Every 5 minutes |
| `agg.device_geofence_status_hist` | A position of a device and a geo object that it is inside | Positions and the shapes of geo objects | Every 15 minutes |

Each run calculates the last 14 days again. A trip or a stop is added after it ends and doesn't change after that, so its `id` is stable and you can store it in your own systems.

Each row already has `workspace_id`, `asset_id`, and `device_id`, so you can filter and group by asset without a join. Join `core_bdr` only when you need names. See [report database](report.md) and [agg database](agg.md) to learn how Navixy calculates each table and which columns it has.

## When to use the transformation layer

Start from the common transformations and query the raw data only when they don't fit:

* **A common transformation answers the question.** Query `report` or `agg` directly. The tables are already calculated, so a query over months of data reads far fewer rows than the same query over `telemetry`.
* **A common transformation is close, but not exact.** Filter or group a `report` or `agg` table in your query. For example, count only the stops longer than one hour.
* **You need a different calculation rule.** Query `telemetry` and `core_bdr` in the [raw data layer](../raw-data-layer/) with your own rule. For example, filtering `report.trips_stops` can't produce trips that follow a different trip rule.

## Tables of the transformation layer

* [report database](report.md): Find the columns of trips and stops, and learn how Navixy detects them.
* [agg database](agg.md): Find the columns of sensor values, digital inputs and outputs, and positions in geo objects.
