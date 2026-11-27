---
description: >-
  Make your first requests with the API of Navixy Reports and Dashboards: copy
  the session token, run a report, and read the rows of a dashboard panel.
tags:
  - guide
  - beta
---

# API with session token

This guide takes you from a signed-in browser session to the full data of a report and a dashboard panel. You copy the session token, list the report types, and run a report. Then you read the rows of a dashboard panel. Every step is a `curl` request that you can run in a terminal.

The API returns the same data that the pages of Reports and Dashboards show. Use it for your own requests and scripts that you run while you work. For an integration that runs on its own, see [Tools with API key](tools-with-api-key.md).

## Prerequisites

Before you start, make sure that you have:

* Access to Navixy Reports and Navixy Dashboards in your workspace, through your role.
* The region of your organization: `eu` for the European Union or `us` for the United States. The examples write it as `<REGION>`.
* A browser with developer tools, and `curl` in a terminal.

## Steps

{% stepper %}
{% step %}
### Copy the session token of Reports

Open `https://reports.<REGION>.navixy.com`, sign in, and choose your workspace. Open the developer tools of the browser, then the **Network** tab, and reload the page. Select a request to `/api/reports` or `/api/me/permissions`, and copy the value of its `Authorization` header after `Bearer`. Don't use the request to `/api/auth/exchange`: it sends the token of your sign-in, which the API refuses.

Save the value in the `SESSION_TOKEN` variable of your terminal:

```bash
export SESSION_TOKEN='<TOKEN>'
```

The token expires after 5 minutes. When a request returns HTTP 401, copy a new token from a newer request of the page. See [API access](../authentication.md#api-access).
{% endstep %}

{% step %}
### List report types

Read the report types that your role lets you view:

```bash
curl --request GET \
  --url 'https://reports.<REGION>.navixy.com/api/reports' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response:

```json
[
  { "id": "driver_performance", "name": "Driver performance", "actions": ["view", "execute", "edit"] },
  { "id": "sensor_readings", "name": "Sensor readings", "actions": ["view", "execute", "edit"] },
  { "id": "trips_and_stops", "name": "Trips and stops", "actions": ["view", "execute", "edit"] }
]
```

`id` names the report type in the next step. `actions` lists what your role allows: `execute` lets you run the report. See [Report types](../reports/report-types.md).
{% endstep %}

{% step %}
### Run a report

Run the Trips and stops report for one day and all your assets:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/reports/trips_and_stops/run' \
  --header "Authorization: Bearer $SESSION_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "from": "2026-11-26T00:00:00+01:00",
    "to": "2026-11-27T00:00:00+01:00"
  }'
```

Response, shortened to 1 trip and 1 stop:

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

Parameters:

* `from` and `to`: the start and the end of the period, as dates and times with an offset. The period is at most 31 days and an hour.
* `assetIds`: a list of asset IDs. Without it, the report covers every asset that your role lets you see.

The response has every trip and stop of the period. `dataUntil` says up to when Reports has data. See [Report runs](../reports/report-runs.md).
{% endstep %}

{% step %}
### Read the rows of a dashboard panel

The Dashboards API needs a session token of Dashboards. Open `https://dashboards.<REGION>.navixy.com`, sign in, and copy the token in the same way as in the first step, from a request to `/api/dashboards`. Save it in the `DASHBOARDS_TOKEN` variable.

Read panel 11 of the Trips dashboard, **Top 10 assets by distance**, for the last 7 days:

```bash
curl --request GET \
  --url 'https://dashboards.<REGION>.navixy.com/api/dashboards/trips/panels/11?range=7d' \
  --header "Authorization: Bearer $DASHBOARDS_TOKEN"
```

Response, shortened to 3 rows:

```json
{
  "rows": [
    { "asset": "NV4-150 · FMC150 (D)", "distance": 12039682 },
    { "asset": "NV4-300 - FMC130 (D)", "distance": 8801829 },
    { "asset": "IST-02 Truck (FMM250)", "distance": 8732564 }
  ]
}
```

Parameters:

* `range`: the period, one of the periods of the dashboard, such as `7d`. Without it, the panel uses the default period of the dashboard.

A panel returns its rows without unit conversion: here, `distance` is in meters. See [Panel data](../dashboards/panel-data.md) and [Dashboard panels](../dashboards/dashboard-panels.md).
{% endstep %}
{% endstepper %}

{% hint style="success" %}
With the session token of your browser, you now read the report types, run a report, and read the rows of a dashboard panel.
{% endhint %}

## Error handling

The steps can return these errors:

* HTTP 401 with no body: the session token is missing or expired, or it's the token of the other product. Copy a new token from the page of the right product.
* HTTP 400 with `invalid_report_parameters`: a field of the run is wrong. `details` names it.
* HTTP 403: your role doesn't include the product, the report type, or the dashboard.

For every code and how to handle it, see [Error handling](../error-handling.md).

## Next steps

* [Reports API reference](../reports/api-reference.md): every operation of the Reports API.
* [Dashboards API reference](../dashboards/api-reference.md): every operation of the Dashboards API.
* [API limits](../limits.md#api-limits): the longest period, the number of assets, and the size of a report.
