---
description: >-
  What the Dashboards API returns: the dashboards of a workspace, their
  periods, and what every panel shows for a period. Its access and limits.
tags:
  - overview
  - beta
---

# Dashboards API overview

The Dashboards API returns the data of the dashboards in Navixy Dashboards. They show the fleet's current status, its trips, and the condition of its vehicles and GPS devices. Your app can list the dashboards that an access token can read and read what every panel of a dashboard shows for a period. For example, an app can show the number of online assets on your website or copy a weekly ranking to a spreadsheet.

This page is for developers who read dashboard data from their own code.

The API is a REST API at `https://dashboards.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. Every request is a `POST` request with an access token in the `Authorization` header and a JSON body, and every response is JSON. To get a token, see [Authentication](../authentication.md).

The following request lists the dashboards and periods that the token can read:

```bash
curl --request POST \
  --url 'https://dashboards.<REGION>.navixy.com/api/tools/context' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Berlin"}'
```

Response:

```json
{
  "now": "2026-11-27T16:07:52+01:00",
  "timeZone": "Europe/Berlin",
  "dashboards": [
    { "code": "live_status", "name": "Live status", "ranges": [] },
    { "code": "trips", "name": "Trips", "ranges": ["1h", "6h", "12h", "24h", "7d", "14d", "31d"] },
    { "code": "conditions", "name": "Conditions", "ranges": ["1h", "6h", "12h", "24h", "7d", "14d", "31d"] }
  ]
}
```

## What you can do with the Dashboards API

* List the dashboards and periods that your access token can read. See [Dashboard data](dashboard-data.md).
* Read what every panel of a dashboard shows for a period of up to 31 days, or for the period before. See [Dashboard data](dashboard-data.md).
* Find the panel that your app needs and the values that it returns. See [Dashboard panels](dashboard-panels.md).
* Look up each operation and its response models. See [Dashboards API reference](api-reference.md).

## Key concepts

<table><thead><tr><th width="160">Term</th><th>Definition</th></tr></thead><tbody><tr><td>Dashboard</td><td>A set of panels about one subject. There are 3 dashboards, with the codes <code>live_status</code>, <code>trips</code>, and <code>conditions</code>. Each workspace has the same 3. To learn what each one shows, see <a href="https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards">Dashboards</a> in the Location Intelligence documentation.</td></tr><tr><td>Panel</td><td>One number, chart, table, or map on a dashboard, such as <strong>Total distance</strong>. A panel has an ID that is unique in its dashboard.</td></tr><tr><td>Period</td><td>The time that Trips and Conditions cover: the last 1, 6, 12, or 24 hours, or the last 7, 14, or 31 days. Live status has no periods.</td></tr><tr><td>Dashboard data</td><td>What every panel of one dashboard shows for one period, with the units of its values.</td></tr></tbody></table>

## How the Dashboards API works

Every operation is a `POST` request with a JSON body, sent to a path under `/api/tools/`. Every body includes `timeZone`, as [Time zone](../README.md#time-zone) describes. An app reads a dashboard in 3 steps:

1. List the dashboards and periods, with `POST /api/tools/context`. It gives the dashboard codes and the period IDs that the token can use.
2. Get the dashboard data for a period, with `POST /api/tools/read_dashboard`.
3. To keep the data current, get the dashboard data again later. Navixy Dashboards reads its panels again every 90 seconds.

[Dashboard data](dashboard-data.md) describes the first 2 steps, and [Dashboards API reference](api-reference.md) has both operations. For a first request from start to end, see [Getting started](../getting-started.md).

### Which data a response includes

The data is the data of one workspace: the workspace of the access token. Inside that workspace, the data includes only the assets, GPS devices, and geo objects that the key's role lets you see. So 2 apps whose keys have different roles can get different values from the same dashboard.

On Trips and Conditions, each period ends at the moment of the request. With `"previous": true`, the data covers the period of the same length right before, so your app can compare the 2 periods. Live status shows the current state of the fleet and the last 24 hours, so a request for its data has no period.

Navixy builds trips every 4 hours. So over a period of a few hours, the Trips panels that count trips can return 0 while your vehicles drive.

## Access to the Dashboards API

The API key's role decides what your app can read. To read a dashboard, your app needs all of the following:

* The workspace has Dashboards.
* The role includes Dashboards, its `dashboards-standard` feature, and the dashboard. Without the feature, both operations return HTTP 403. A dashboard that the role doesn't grant returns HTTP 400, as [Dashboards API errors](errors.md#how-to-handle-a-dashboard-that-the-role-doesnt-grant) describes.
* For the values, the role lets you see the assets, GPS devices, and geo objects.

To change a role, ask your organization's administrator. See [Access to Dashboards](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards#access-to-dashboards) in the Location Intelligence documentation.

## Limits of the Dashboards API

The limits of the Analytics API apply to the Dashboards API. Each request for dashboard data counts toward the daily limit, and listing the dashboards and periods doesn't. See [Daily limits](../limits.md#daily-limits) and [Request limits](../limits.md#request-limits).

## Section contents

* [Dashboard data](dashboard-data.md): List the dashboards and periods that the token can read, then read what every panel of a dashboard shows.
* [Dashboard panels](dashboard-panels.md): What each panel of Live status, Trips, and Conditions returns.
* [Dashboards API reference](api-reference.md): Every operation and its response models.
* [Dashboards API errors](errors.md): The error details and the counting of dashboard reads that are specific to the Dashboards API.
