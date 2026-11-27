---
description: >-
  Make your first requests to the Analytics API: get an access token from an
  API key, list report types, get a report summary, and read dashboard data.
tags:
  - guide
  - beta
---

# Getting started

This guide takes you from an API key to your first data from Navixy Reports and Dashboards. You get an access token, list the report types of your workspace, get the summary of a report, and read the data of a dashboard. Every step is a `curl` request that you can run in a terminal.

## Prerequisites

Before you start, make sure that you have:

* An API key from Navixy Console. Your organization's administrator creates it. The key's role must include Reports and Dashboards. See [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Navixy Console documentation.
* The ID of the workspace, if the key's role covers more than one workspace. Navixy Console shows it in the example request next to a new key.
* The region of your organization: `eu` for the European Union or `us` for the United States. The examples write it as `<REGION>`.
* `curl` in a terminal.

## Steps

{% stepper %}
{% step %}
### Get an access token

Exchange the API key for an access token at the Navixy identity service:

```bash
curl --request POST \
  --url 'https://iam.<REGION>.navixy.com/realms/quickstart/protocol/openid-connect/token' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:token-exchange' \
  --data-urlencode 'client_id=api-key-exchange' \
  --data-urlencode 'subject_token=<API_KEY>' \
  --data-urlencode 'subject_token_type=urn:navixy:token-type:api-key' \
  --data-urlencode 'navixy_agent_name=fleet-report-exporter' \
  --data-urlencode 'navixy_workspace_id=<WORKSPACE_ID>'
```

Response:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhY3Rvcl9pZCI6IjAxOWE2YjJmIn0.c2lnbmF0dXJl",
  "expires_in": 300,
  "token_type": "Bearer",
  "issued_token_type": "urn:ietf:params:oauth:token-type:access_token"
}
```

Parameters:

* `subject_token`: your whole API key, including `nvx_`.
* `navixy_agent_name`: a name for your app, 1 to 100 characters.
* `navixy_workspace_id`: the ID of the workspace. Leave it out when the key's role covers one workspace.

Save `access_token` in the `ACCESS_TOKEN` variable of your terminal. The token expires after the number of seconds in `expires_in`. Then exchange the key again. See [Authentication](authentication.md).
{% endstep %}

{% step %}
### List report types and assets

Read the report types and assets that your token can use, and the dates of common periods:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/context' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Berlin"}'
```

Response, shortened to 2 periods and without the descriptions of the report types:

```json
{
  "today": "2026-11-27",
  "timeZone": "Europe/Berlin",
  "periods": {
    "yesterday": { "from": "2026-11-26", "to": "2026-11-26" },
    "last_week": { "from": "2026-11-16", "to": "2026-11-22" }
  },
  "templates": [
    { "code": "driver_performance", "name": "Driver performance" },
    { "code": "sensor_readings", "name": "Sensor readings" },
    { "code": "trips_and_stops", "name": "Trips and stops" }
  ],
  "assets": [],
  "assetCount": 0
}
```

Parameters:

* `timeZone`: the time zone of your app's user, such as `Europe/Berlin`. Every request includes it. See [Time zone](README.md#time-zone).

`templates` lists the report types by their `code`, which the next step names. `assets` lists the assets that you can see and that Reports has GPS device data for. This workspace has none yet. See [Report types and assets](reports/report-types-and-assets.md).
{% endstep %}

{% step %}
### Get a report summary

Run the Trips and stops report for last week and all your assets:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/run_report_totals' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "timeZone": "Europe/Berlin",
    "report": "trips_and_stops",
    "from": "2026-11-16",
    "to": "2026-11-22",
    "assets": "all"
  }'
```

Response:

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

Parameters:

* `report`: the `code` of a report type from the previous step.
* `from` and `to`: the first and the last day of the period, both included.
* `assets`: `"all"`, or a list of asset IDs.

A report summary has the figures of the report's **Summary** tab in Location Intelligence. `totals` has them for all assets, and `assets` for each asset. Each request is one report run. An API key can make about 250 runs in a workspace in 24 hours. See [Report summary](reports/report-summary.md).
{% endstep %}

{% step %}
### Read dashboard data

Read the Live status dashboard:

```bash
curl --request POST \
  --url 'https://dashboards.<REGION>.navixy.com/api/tools/read_dashboard' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Berlin", "dashboard": "live_status"}'
```

Response, shortened to the first panel:

```json
{
  "dashboard": "live_status",
  "name": "Live status",
  "range": null,
  "previous": false,
  "from": null,
  "to": null,
  "panels": [
    {
      "id": 1,
      "type": "kpi",
      "title": "Total assets",
      "description": "Total number of unique fleet assets registered for the selected client.",
      "fields": [{ "key": "value" }],
      "rows": [{ "value": 0 }]
    }
  ]
}
```

Parameters:

* `dashboard`: the code of the dashboard: `live_status`, `trips`, or `conditions`.
* `range`: the period, such as `7d`. Live status shows the current state, so it has no period. For Trips and Conditions, add `"range": "7d"` to read the last 7 days.

The response has every panel of the dashboard, with the same values that the panel shows in Location Intelligence. See [Dashboard data](dashboards/dashboard-data.md).
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Your app now gets an access token, reads the report types and assets, gets a report summary, and reads a dashboard. The same token works for both APIs until it expires.
{% endhint %}

## Error handling

The steps can return these errors:

* HTTP 401 with no body: the token is missing or expired. Exchange the key again.
* HTTP 400 with `invalid_tool_call`: a field of the body is wrong. `details` names it. For example, an offset such as `+01:00` in `timeZone` returns `"timeZone": "the user's IANA time zone, such as Europe/Madrid, not an offset"`.
* HTTP 403: the workspace doesn't have the product, or the key's role doesn't include it.

For every code and how to handle it, see [Error handling](error-handling.md).

## Next steps

* [Reports API reference](reports/api-reference.md): every operation of the Reports API.
* [Dashboards API reference](dashboards/api-reference.md): every operation of the Dashboards API.
* [Limits](limits.md): how many requests you can make, and the limits of each request.
