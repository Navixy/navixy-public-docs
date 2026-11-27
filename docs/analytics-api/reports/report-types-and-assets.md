---
description: >-
  List the report types that your access token can run in Navixy Reports, the
  assets it can see, and the dates of common periods.
tags:
  - fundamentals
  - beta
---

# Report types and assets

The list of report types and assets tells your app what it can use in Reports. It has the report types that your token can run and the assets that it can see. It also has today's date and the dates of 6 common periods in your time zone. Get the list before you request a report summary or a link, and take the report type IDs and the asset IDs from it. The operation is `POST /api/tools/context`. See [List report types and assets](api-reference.md) in the API reference.

{% hint style="info" %}
**Access**

The workspace must have Reports, and your token's role must give access to it. See [Access to the Reports API](README.md#access-to-the-reports-api).
{% endhint %}

## What the list contains

| Field | What it stores |
| --- | --- |
| `today` | Today's date in `timeZone`. A period of the other operations can't end after it |
| `periods` | The first and the last day of `today`, `yesterday`, `this_week`, `last_week`, `this_month`, and `last_month`. A week starts on Monday, and this week and this month end today |
| `templates` | The report types that your token's role lets you run, each with its `code`, its `name`, and a `description` of what the other operations return for it |
| `assets` | The assets that you can see and that Reports has GPS device data for, each with its `id` and its `title`, when there are at most 200 of them |
| `assetCount` | The number of these assets |

The body has only `timeZone`. Send this body to `POST /api/tools/context` with the headers that [Authentication](../authentication.md#send-the-access-token) shows:

```json
{
  "timeZone": "Europe/Berlin"
}
```

Response, with the descriptions of the report types shortened:

```json
{
  "today": "2026-11-27",
  "timeZone": "Europe/Berlin",
  "periods": {
    "today": { "from": "2026-11-27", "to": "2026-11-27" },
    "yesterday": { "from": "2026-11-26", "to": "2026-11-26" },
    "this_week": { "from": "2026-11-23", "to": "2026-11-27" },
    "last_week": { "from": "2026-11-16", "to": "2026-11-22" },
    "this_month": { "from": "2026-11-01", "to": "2026-11-27" },
    "last_month": { "from": "2026-10-01", "to": "2026-10-31" }
  },
  "templates": [
    {
      "code": "driver_performance",
      "name": "Driver performance",
      "description": "Each asset's driving violations over a period."
    },
    {
      "code": "sensor_readings",
      "name": "Sensor readings",
      "description": "What each sensor on each asset read over a period, in the unit its device reports."
    },
    {
      "code": "trips_and_stops",
      "name": "Trips and stops",
      "description": "Each asset's trips and stops over a period."
    }
  ],
  "assets": [
    { "id": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f01", "title": "Van 01 (FMB920)" },
    { "id": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f02", "title": "Truck 02 (FMC130)" },
    { "id": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f03", "title": "Bus 03 (FMB125)" }
  ],
  "assetCount": 3
}
```

## How to use the list

* **More than 200 assets.** The response has no `assets` list. Send `"all"` in the `assets` of the other operations, or take the asset IDs from [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/). The asset IDs are the same in both APIs.
* **Assets without data.** An asset that Reports has no GPS device data for yet isn't in the list. The other operations return HTTP 400 for its ID.
* **No report types.** An empty `templates` list means that your role lets you run no report type. See [How to handle a role without report types](errors.md#how-to-handle-a-role-without-report-types).
* **The workspace.** The token decides the workspace. A body with any field but `timeZone`, such as `workspace_id`, returns HTTP 400 with `invalid_tool_call`.

The request doesn't count as a report run. See [Daily limits](../limits.md#daily-limits). The errors use the format of every Reports API error. See [Reports API errors](errors.md).
