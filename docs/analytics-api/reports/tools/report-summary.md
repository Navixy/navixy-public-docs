---
description: >-
  Get the summary of a Navixy report for a period and a set of assets: the
  figures of the Summary tab, for all assets and for each asset or sensor.
tags:
  - fundamentals
  - beta
---

# Report summary

{% hint style="info" %}
**Tools, with an API key**

The operation on this page is a tool. It accepts the access token that your app gets by exchanging a Navixy Console API key. The role must let you run the report type in `report`. The summary includes only the assets and GPS devices that the role lets you see. See [Tools access](../../authentication.md#tools-access).
{% endhint %}

A report summary is the set of summary figures of a report for a period. For Trips and stops, these are the figures in the tiles of the report's **Summary** tab in Location Intelligence. The summary has them for all assets together and for each asset. For Sensor readings, they are each sensor's average, lowest, highest, first, and last reading. A report summary never returns individual trips, stops, or readings.

The operation is `POST /api/tools/run_report_totals`. See [Get report summary](README.md#tools) in the Reports tools reference.

## What a report summary contains

The body names the report type in `report`, the period in `from` and `to`, and the assets in `assets`. The summary depends on the report type:

| Report type | What the summary contains |
| --- | --- |
| `trips_and_stops` | `totals`: the number of trips and stops, the distance, the driving time, the stop time, and the maximum speed of all assets. `assets`: the same figures for each asset with a trip or a stop, the longest distance first |
| `sensor_readings` | The number of assets, GPS devices, sensors, and readings. `series`: one line for each sensor, with its average, lowest, highest, first, and last reading, its change, and its number of readings |
| `driver_performance` | No summary. The request returns HTTP 400 with `invalid_tool_call` |

The following request asks for the Trips and stops summary of all assets for one day:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/run_report_totals' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "timeZone": "Europe/Berlin",
    "report": "trips_and_stops",
    "from": "2026-11-27",
    "to": "2026-11-27",
    "assets": "all"
  }'
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

Response to a request for one asset on 26 November, a day with trips:

```json
{
  "totals": {
    "trips": 16,
    "distanceM": 186300,
    "drivingS": 48600,
    "stops": 17,
    "stopS": 5520,
    "maxSpeedKmh": 40.0
  },
  "assetsInScope": 1,
  "assets": [
    {
      "assetId": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f01",
      "title": "Van 01 (FMB920)",
      "figures": {
        "trips": 16,
        "distanceM": 186300,
        "drivingS": 48600,
        "stops": 17,
        "stopS": 5520,
        "maxSpeedKmh": 40.0
      }
    }
  ],
  "assetsOmitted": 0
}
```

## How to read the figures

* **Assets without trips.** `assetsInScope` counts every asset of the report, including the assets without trips or stops. With no trip, the distance and the driving time are 0, and the maximum speed is `null`.
* **Change of a sensor.** `change` is the last reading minus the first: how much a counter, such as mileage, grew.
* **Sensor keys.** A key such as `avl_io_181` is a code that the GPS device sends, and Reports doesn't know what it measures.
* **Long lists.** A summary has at most 50 asset or sensor lines. `assetsOmitted` or `seriesOmitted` counts the lines after them. To read more of them, request the summary for fewer assets.

Each request counts toward the daily limit. See [Daily limits](../../limits.md#tools-limits) and [Reports errors](../errors.md#tools-errors).
