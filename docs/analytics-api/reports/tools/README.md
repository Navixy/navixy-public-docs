---
description: >-
  The Reports tools for integrations and AI agents: report types and assets,
  report summaries, and links to reports, with a Navixy Console API key.
tags:
  - reference
  - beta
---

# Reports tools

{% hint style="info" %}
**Tools, with an API key**

The operations on this page are tools. They accept the access token that your app gets by exchanging a Navixy Console API key. Every body includes `timeZone`: see [Time zone](../../README.md#time-zone). The workspace must have Reports, and the role must give access to it. See [Tools access](../../authentication.md#tools-access).
{% endhint %}

The Reports tools are the operations under `/api/tools/` of Reports. They accept the access token of an API key, so an integration or an AI agent can use them without a signed-in browser. This page is the reference of the 3 tools, with their requests, their responses, and the response models.

The base address is `https://reports.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. The 3 tools:

* `POST /api/tools/context` lists the report types and the assets that the token can use. See [Report types and assets](#report-types-and-assets).
* `POST /api/tools/run_report_totals` returns the summary of a report. See [Report summary](report-summary.md).
* `POST /api/tools/propose_report` returns a link that opens a report in Location Intelligence. See [Links to reports](links-to-reports.md).

## Report types and assets

`POST /api/tools/context` tells your app what it can use in Reports. It has the report types that your token can run and the assets that it can see. It also has today's date and the dates of 6 common periods in your time zone. Call it before the other tools, and take the report type IDs and the asset IDs from it.

### What the list contains

| Field | What it stores |
| --- | --- |
| `today` | Today's date in `timeZone`. A period of the other operations can't end after it |
| `periods` | The first and the last day of `today`, `yesterday`, `this_week`, `last_week`, `this_month`, and `last_month`. A week starts on Monday, and this week and this month end today |
| `templates` | The report types that your token's role lets you run, each with its `code`, its `name`, and a `description` of what the other operations return for it |
| `assets` | The assets that you can see and that Reports has GPS device data for, each with its `id` and its `title`, when there are at most 200 of them |
| `assetCount` | The number of these assets |

The body has only `timeZone`. The following request lists the report types and assets:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/context' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Berlin"}'
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

### How to use the list

* **More than 200 assets.** The response has no `assets` list. Send `"all"` in the `assets` of the other operations, or take the asset IDs from [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/). The asset IDs are the same in both APIs.
* **Assets without data.** An asset that Reports has no GPS device data for yet isn't in the list. The other operations return HTTP 400 for its ID.
* **No report types.** An empty `templates` list means that your role lets you run no report type. See [Report types that your role doesn't grant](../errors.md#report-types-that-your-role-doesnt-grant).
* **The workspace.** The token decides the workspace. A body with any field but `timeZone`, such as `workspace_id`, returns HTTP 400 with `invalid_tool_call`.

The request doesn't count toward the daily limit. See [Tools limits](../../limits.md#tools-limits).

## Tools

{% openapi-operation spec="analytics-reports" path="/tools/context" method="post" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/tools/run_report_totals" method="post" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/tools/propose_report" method="post" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-operation %}

## Response models

{% openapi-schemas spec="analytics-reports" schemas="ReportTypesAndAssets" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Period" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ReportTypeInfo" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="AssetInfo" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TripsSummary" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TripFigures" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="AssetTrips" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorSummary" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorLine" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ReportLink" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="View" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Error" grouped="false" %}
[OpenAPI analytics-reports](../../reference/Reports.json)
{% endopenapi-schemas %}
