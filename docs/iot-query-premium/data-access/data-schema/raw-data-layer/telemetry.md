---
description: >-
  The telemetry database of IoT Query Premium: the time, position, speed,
  odometer, and sensor values of each message that your GPS devices send.
tags:
  - reference
  - preview
---

# telemetry database

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

The `telemetry` database has the messages that your GPS devices send to Navixy: the time, position, speed, odometer, and the value of every sensor in the message. It belongs to the [raw data layer](./), so each row is a message as the device sent it. Navixy calculates nothing from other messages. This page describes the columns of `telemetry.records`, explains how sensor values are stored, and gives example queries. It is for developers and analysts who need every data point or a sensor that the [transformation layer](../transformation-layer/) doesn't aggregate.

## Table telemetry.records

`telemetry.records` has one row per message from a GPS device.

| Column | Type | What it contains |
| --- | --- | --- |
| `device_id` | `UUID` | The GPS device, the same ID as `core_bdr.device.id` |
| `device_time` | `DateTime64(3)` | The time when the device recorded the message, in UTC |
| `latitude` | `Float64` | Latitude in degrees, negative in the southern hemisphere |
| `longitude` | `Float64` | Longitude in degrees, negative in the western hemisphere |
| `speed` | `Float32` | Speed in km/h |
| `hw_mileage_m` | `Nullable(Float64)` | The odometer of the device, in meters. `NULL` if the device doesn't send it |
| `field_float` | `Map(String, Float64)` | Sensor values with a fraction, by signal name |
| `field_int` | `Map(String, Int64)` | Whole-number sensor values, by signal name |
| `field_bool` | `Map(String, UInt8)` | On and off values, such as ignition, by signal name: `1` or `0` |
| `field_unit` | `Map(String, String)` | The unit of each value, such as `L`, `°C`, or `rpm` |
| `field_type` | `Map(String, String)` | The type of each signal, as the device declares it: `FLOAT64`, `UINT64`, or `BOOL`. The value itself is stored in `field_float`, `field_int`, or `field_bool` |

`telemetry.records` has the device ID but no asset ID. To find the asset of a device, join `core_bdr.custom_field_value_device`, as described in [Data schema](../#how-tables-join).

## How sensor values are stored

A message can have dozens of sensor values, and each GPS device model sends different sensor values. `telemetry.records` doesn't have a column per sensor. It stores the values in maps, where each key is the name of a signal and each value is its reading. For example, a fuel level sensor can appear as the key `lls_level_0` in `field_float`. The same key in `field_unit` has the unit `L`.

Read one signal with its key:

* `field_float['lls_level_0']` returns the value, or `0` if the message doesn't have the signal.
* `mapContains(field_float, 'lls_level_0')` returns `1` if the message has the signal. Filter on it so that a missing value isn't read as `0`.
* `field_bool['ignition']` returns `1` when the ignition is on.

The signal names come from the GPS device. To find the names that your devices send, list the keys of recent messages:

```sql
SELECT DISTINCT arrayJoin(mapKeys(field_float)) AS signal
FROM telemetry.records
WHERE device_time >= now() - INTERVAL 1 DAY
ORDER BY signal
```

The result has one row per signal name:

| signal |
| --- |
| `battery_voltage` |
| `external_voltage` |
| `lls_level_0` |
| `temperature_0` |

Navixy calibrates the sensor values before it writes a message, so the maps store each value in its unit. For example, Navixy stores the value of a fuel level sensor in liters, not in the raw units of the sensor. Calibration is the only change to a message before it is written.

## Example queries

### Last known position of each device

The following query returns the latest position of each device that sent a message in the last hour:

```sql
SELECT
    device_id,
    max(device_time) AS last_seen,
    argMax(latitude, device_time) AS latitude,
    argMax(longitude, device_time) AS longitude,
    argMax(speed, device_time) AS speed_kmh
FROM telemetry.records
WHERE device_time >= now() - INTERVAL 1 HOUR
GROUP BY device_id
```

The result has one row per device:

| device_id | last_seen | latitude | longitude | speed_kmh |
| --- | --- | --- | --- | --- |
| 5f0c2b8e-1d3a-4c7e-9a41-2e6b7d8f9a10 | 2026-09-28 14:02:31.000 | 52.370216 | 4.895168 | 42 |
| 9b7e1c4d-6a2f-4e8b-b3c5-0d1f2a3b4c5d | 2026-09-28 14:01:57.000 | 52.090737 | 5.12142 | 0 |

For the current state of every device, including devices that sent nothing recently, read `report.device_status` in the [report database](../transformation-layer/report.md).

### Values of one sensor over a day

The following query returns every fuel level reading of one device over the last day:

```sql
SELECT
    device_time,
    field_float['lls_level_0'] AS fuel_level,
    field_unit['lls_level_0'] AS unit
FROM telemetry.records
WHERE device_id = toUUID('5f0c2b8e-1d3a-4c7e-9a41-2e6b7d8f9a10')
  AND device_time >= now() - INTERVAL 1 DAY
  AND mapContains(field_float, 'lls_level_0')
ORDER BY device_time
```

The result has one row per message that has the sensor:

| device_time | fuel_level | unit |
| --- | --- | --- |
| 2026-09-27 14:05:12.000 | 182.4 | L |
| 2026-09-27 14:06:12.000 | 182.1 | L |
| 2026-09-27 14:07:12.000 | 181.9 | L |

For values grouped by minute, hour, or day, read `report.sensor_readings` and `report.sensor_readings_rollup` instead. Navixy calculates these tables from the same messages.

## See also

* [core_bdr database](core-bdr.md): Find the devices and assets that telemetry belongs to.
* [report database](../transformation-layer/report.md): Find the trips, visits, and sensor periods that Navixy calculates from telemetry.
