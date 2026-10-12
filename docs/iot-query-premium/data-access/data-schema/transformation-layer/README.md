---
description: >-
  The transformation layer of IoT Query Premium: tables that Navixy calculates
  from raw data, such as trips and geo object visits, and when to query them.
tags:
  - fundamentals
  - preview
---

# Transformation layer

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Navixy calculates the tables of the transformation layer for you from the [raw data layer](../raw-data-layer/). The tables cover trips and stops, geo object visits, sensor values per period, and the state of each device. This page lists the tables and explains when to query them instead of the raw data. It is for developers and analysts who choose which tables to query.

## Common transformations

Navixy calculates a set of tables for every workspace, in the `report` database. You don't configure them: they are ready as soon as your GPS devices send data.

| Table | One row is | Calculated from |
| --- | --- | --- |
| `report.trips_stops` | A trip or a stop of an asset | Positions, speed, and ignition in `telemetry` |
| `report.device_geofence_status_hist` | An entry to or an exit from a geo object | Positions and the shapes of geo objects |
| `report.device_geofence_status` | A geo object that a device is in now | The latest position of each device and the shapes of geo objects |
| `report.sensor_readings` | The values of one sensor over 1 minute | Sensor values in `telemetry` |
| `report.sensor_readings_rollup` | The values of one sensor over 10 minutes, 30 minutes, 1 hour, 4 hours, or 1 day | The 1-minute sensor periods |
| `report.device_status_hist` | The state of a device at one position | Positions and the time since the last message |
| `report.device_status` | The current state of a device | The latest state of each device |

Each row already has `org_id`, `workspace_id`, `asset_id`, and `device_id`, so you can filter and group by asset without a join. Join `core_bdr` only when you need names. See [report database](report.md) to learn how Navixy calculates each table and which columns it has.

Navixy recalculates the tables of trips, stops, geo object visits, and sensor periods on a schedule. Each run calculates a recent period again, because a trip or a visit that is still in progress isn't final. The device status tables are written as the messages arrive. A completed trip keeps its `id` across runs, so you can store the ID in your own systems.

## When to use the transformation layer

Start from the common transformations and query the raw data only when they don't fit:

* **A common transformation answers the question.** Query `report` directly. The tables are already calculated, so a query over months of data reads far fewer rows than the same query over `telemetry`.
* **A common transformation is close, but not exact.** Filter or group a `report` table in your query. For example, count only the stops longer than one hour.
* **You need a different calculation rule.** Query `telemetry` and `core_bdr` in the [raw data layer](../raw-data-layer/) with your own rule. For example, filtering `report.trips_stops` can't produce trips that follow a different trip rule.

## Tables of the transformation layer

* [report database](report.md): Find the columns of trips, geo object visits, sensor readings, and device status, and learn how Navixy calculates each table.
