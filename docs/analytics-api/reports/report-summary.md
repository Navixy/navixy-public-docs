---
description: >-
  Get the summary of a Navixy report for a period and a set of assets: the
  figures of the Summary tab, for all assets and for each asset or sensor.
tags:
  - fundamentals
  - beta
---

# Report summary

A report summary is the set of summary figures of a report for a period. For Trips and stops, these are the figures in the tiles of the report's **Summary** tab in Location Intelligence. The summary has them for all assets together and for each asset. For Sensor readings, they are each sensor's average, lowest, highest, first, and last reading. A report summary never returns individual trips, stops, or readings.

The operation is `POST /api/tools/run_report_totals`. See [Get report summary](api-reference.md) in the API reference.

{% hint style="info" %}
**Access**

Your token's role must let you run the report type in `report`. The summary includes only the assets and GPS devices that the role lets you see. See [Access to the Reports API](README.md#access-to-the-reports-api).
{% endhint %}

## What a report summary contains

The body names the report type in `report`, the period in `from` and `to`, and the assets in `assets`. The summary depends on the report type:

| Report type | What the summary contains |
| --- | --- |
| `trips_and_stops` | `totals`: the number of trips and stops, the distance, the driving time, the stop time, and the maximum speed of all assets. `assets`: the same figures for each asset with a trip or a stop, the longest distance first |
| `sensor_readings` | The number of assets, GPS devices, sensors, and readings. `series`: one line for each sensor, with its average, lowest, highest, first, and last reading, its change, and its number of readings |
| `driver_performance` | No summary. The request returns HTTP 400 with `invalid_tool_call` |

The following body asks for the Trips and stops summary of all assets for one day. Send this body to `POST /api/tools/run_report_totals` with the headers that [Authentication](../authentication.md#send-the-access-token) shows:

```json
{
  "timeZone": "Europe/Berlin",
  "report": "trips_and_stops",
  "from": "2026-11-27",
  "to": "2026-11-27",
  "assets": "all"
}
```

Response, in a workspace without assets:

```json
{
  "totals": {
    "trips": 0,
    "distanceM": 0,
    "drivingS": 0,
    "stops": 0,
    "stopS": 0,
    "maxSpeedKmh": null
  },
  "assetsInScope": 0,
  "assets": [],
  "assetsOmitted": 0
}
```

For a summary with figures, see [Reports API overview](README.md#how-the-reports-api-works).

## How to read the figures

* **Assets without trips.** `assetsInScope` counts every asset of the report, including the assets without trips or stops. With no trip, the distance and the driving time are 0, and the maximum speed is `null`.
* **Change of a sensor.** `change` is the last reading minus the first: how much a counter, such as mileage, grew.
* **Sensor keys.** A key such as `avl_io_181` is a code that the GPS device sends, and Reports doesn't know what it measures.
* **Long lists.** A summary has at most 50 asset or sensor lines. `assetsOmitted` or `seriesOmitted` counts the lines after them. To read more of them, request the summary for fewer assets.

Each request is one report run. See [Daily limits](../limits.md#daily-limits) and [Reports API errors](errors.md).
