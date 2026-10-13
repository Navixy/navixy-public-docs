---
description: >-
  The raw data layer of IoT Query Premium: business data copied from Business
  Data Repository and the telemetry your GPS devices send, before any
  calculation.
tags:
  - fundamentals
  - preview
---

# Raw data layer

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

The raw data layer stores your data as it arrives in IoT Query Premium, before Navixy calculates anything from it. It has two databases: `core_bdr` for business data and `telemetry` for what your GPS devices send. This page explains what the raw data layer contains and when to query it instead of the [transformation layer](../transformation-layer/). It is for developers and analysts who need data at its most detailed.

## What the raw data layer contains

Each database of the raw data layer has one source:

* **`core_bdr`** is a copy of [Business Data Repository](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/bdr): your workspace and its users, devices, assets, asset groups, geo objects, schedules, catalogs, and custom field definitions. Navixy copies it automatically. Each table keeps the columns of Business Data Repository, so an asset in `core_bdr.asset` has the same ID and name as in Business Data Repository.
* **`telemetry`** stores the messages of your GPS devices: time, position, speed, ignition, odometer, and sensor values. Navixy writes each message as it arrives.

Navixy calculates nothing from other rows in the raw data layer. A position in `telemetry` is the position that the device sent. The only change before Navixy writes a message is the calibration of its sensor values into their units. A trip, a stop, or a visit to a geo object doesn't exist in the raw data layer: it is a result of the transformation layer.

## When to query raw data

Query the raw data layer in these cases:

* **You need names, types, or custom fields.** Only `core_bdr` has the names of assets, devices, and geo objects. Join it to any calculated table to show names instead of IDs.
* **You need every data point.** `telemetry` has each message, while the transformation layer groups them into trips, stops, and 1-minute periods.
* **You need a value that no calculated table has**, such as a sensor that the transformation layer doesn't aggregate.
* **You define your own rules.** To detect trips with your own speed limit, or to count events that Navixy doesn't calculate, start from `telemetry`.

For totals, durations, and time in geo objects, query the transformation layer. Its tables are smaller and are already calculated, so the same question takes less time and less SQL.

## Databases of the raw data layer

* [core_bdr database](core-bdr.md): Find the tables of the workspaces, users, devices, assets, asset groups, geo objects, schedules, catalogs, and custom fields.
* [telemetry database](telemetry.md): Find the columns that store positions and sensor values.
