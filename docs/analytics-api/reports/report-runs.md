---
description: >-
  Run a whole Navixy report for a period and a set of assets with the Reports
  API: its trips, stops, sensor series, and driving violations.
tags:
  - fundamentals
  - beta
---

# Report runs

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Reports in a browser. The role must let you run the report type in the path. The run includes only the assets and GPS devices that the role lets you see. See [API access](../authentication.md#api-access).
{% endhint %}

A report run returns a whole report for a period and a set of assets. It has every trip and stop, the sensor series, or the driving violations, as the Reports pages in Location Intelligence show them. This page explains what a run returns and what limits apply. The operation is `POST /api/reports/{id}/run`. See [Run report](api-reference.md) in the API reference.

## What a report run returns

The body has the same fields for every report type: the period in `from` and `to`, and the assets in `assetIds`. Sensor readings also accept `stepSeconds` and `timeZone`. Any other field returns HTTP 400 with `invalid_report_parameters`, so a misspelled field never widens a report to every asset.

| Report type | What the result has |
| --- | --- |
| `trips_and_stops` | `trips` and `stops`: each trip and stop that starts in the period |
| `sensor_readings` | `stepSeconds`: the length of each step, and `series`: one series for each sensor of each GPS device, with one point for each step that has readings |
| `driver_performance` | `trips` and `stops` as `trips_and_stops` returns them, `evaluations`: how each trip and stop was checked for driving violations, and `episodes`: the violations found |

Every result also has `dataFrom` and `dataUntil`, the period for which Reports has data for your assets. Before `dataFrom`, a report is empty because there is no data, not because the assets didn't move.

The following request runs Trips and stops for one asset and one day:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/reports/trips_and_stops/run' \
  --header "Authorization: Bearer $SESSION_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "from": "2026-11-26T00:00:00+01:00",
    "to": "2026-11-27T00:00:00+01:00",
    "assetIds": ["019a7f3e-5c21-7a10-8000-0b2c4d6e8f01"]
  }'
```

Parameters:

* `from` and `to`: The day of 26 November 2026 in Central European Time, as RFC 3339 dates and times with an offset. `from` is included and `to` isn't, so a trip that starts at midnight on 27 November isn't in the report.
* `assetIds`: One asset. Leave it out to include every asset that your token lets you see.

Response, with the lists shortened to the first trip and the first stop:

```json
{
  "trips": [
    {
      "id": "5f1c2a9e7b3d4e6f8a0b1c2d3e4f5a6b",
      "assetId": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f01",
      "deviceId": "019a7f3e-5b9d-7c40-8000-1a2b3c4d5e01",
      "start": "2026-11-26T01:25:35.397Z",
      "end": "2026-11-26T02:09:29.386Z",
      "durationS": 2633,
      "movingS": 1894,
      "parkedS": 731,
      "distanceM": 17127,
      "avgSpeedKmh": 32.55396,
      "maxSpeedKmh": 40,
      "startLat": 44.822908,
      "startLon": 20.45738,
      "endLat": 44.799256,
      "endLon": 20.476289
    }
  ],
  "stops": [
    {
      "id": "9e8d7c6b5a4f3e2d1c0b9a8f7e6d5c4b",
      "assetId": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f01",
      "deviceId": "019a7f3e-5b9d-7c40-8000-1a2b3c4d5e01",
      "start": "2026-11-26T01:19:48.399Z",
      "end": "2026-11-26T01:25:20.374Z",
      "durationS": 331,
      "lat": 44.822344,
      "lon": 20.458412
    }
  ],
  "dataFrom": "2026-11-09T15:59:59.852Z",
  "dataUntil": "2026-11-27T12:00:00Z"
}
```

The response gives every time in UTC. Each field name ends with its unit of measure: `S` for seconds, `M` for meters, and `Kmh` for kilometers per hour. A field that Reports has no value for is `null`.

## How to read sensor readings and driving violations

In a `sensor_readings` result, each point has the lowest, highest, first, and last reading of its step. It also has the sum of the readings and their number. Divide `sum` by `samples` to get the average. `timeZone` is an IANA time zone, such as `Europe/Berlin`. With it, the steps follow the clock of that time zone: a step of 1 day starts at midnight there. Without it, the steps follow UTC.

In a `driver_performance` result, count the violations from `episodes`. A trip or a stop without an entry in `evaluations` isn't checked yet, which doesn't mean that it has no violations. An entry with an empty `kinds` list means that the workspace has no violation rule for it. To learn how violations are counted, see [Driver performance](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/driver-performance) in the Location Intelligence documentation.

## Which data a run includes

* **Assets and devices.** A run includes only the assets of the workspace that the role lets you see. For each asset, it includes only the GPS devices that the role lets you see. An asset ID that the role doesn't let you see adds nothing to the report and returns no error. To list the asset IDs, see [Assets and permissions](assets-and-permissions.md).
* **The period.** A trip or a stop belongs to the period in which it starts.
* **New data.** Reports loads new trips and readings in blocks of 4 hours. `dataUntil` is the end of the last block that it loaded. A trip that is still in progress at that time isn't in the report yet.
* **Old data.** Reports keeps report data for a limited time. See [How far back report data goes](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/how-report-data-works#how-far-back-report-data-goes) in the Location Intelligence documentation.

A run never returns a shortened result. A report that is over the limits of Reports returns HTTP 422 with `report_too_large`. For the longest period, the number of assets, and the other limits of a run, see [API limits](../limits.md#api-limits). To read the track of a trip, see [Trip tracks](trip-tracks.md).

The errors use the format of every Reports error. See [Reports errors](errors.md#api-errors).
