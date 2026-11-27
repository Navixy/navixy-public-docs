---
description: >-
  Create a link that opens a Navixy report in Location Intelligence with the
  period, the assets, and the view that you chose.
tags:
  - fundamentals
  - beta
---

# Links to reports

{% hint style="info" %}
**Tools, with an API key**

The operation on this page is a tool. It accepts the access token that your app gets by exchanging a Navixy Console API key. The role must let you run the report type in `report`, and the person who opens the link sees the report with their own role. See [Tools access](../../authentication.md#tools-access).
{% endhint %}

A link to a report opens the report in Location Intelligence with the period, the assets, and the view that your app chose. Use it to send a person from your app to a report, for example in a message or an email. Reports checks the report as Location Intelligence opens it and returns the checked report with `link`. The operation is `POST /api/tools/propose_report`. See [Create link to report](README.md#tools) in the Reports tools reference.

## How to create a link to a report

The body has the fields of a report summary: `report`, `from`, `to`, and `assets`, with at most 200 asset IDs. `view` sets the view options of the report page:

| Report type | View options |
| --- | --- |
| `trips_and_stops` | `tab`: `days` for the summary day by day, or the ID of an asset of the report for its tab. `kind`: `trip` or `stop` to show only that kind of line in the log |
| `sensor_readings` | `step`: the length of each point of the table and the chart, in seconds |
| `driver_performance` | None |

The following request asks for a link to the trips of one asset on one day:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/propose_report' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "timeZone": "Europe/Berlin",
    "report": "trips_and_stops",
    "from": "2026-11-26",
    "to": "2026-11-26",
    "assets": ["019a7f3e-5c21-7a10-8000-0b2c4d6e8f01"],
    "view": { "kind": "trip" }
  }'
```

Response:

```json
{
  "report": "trips_and_stops",
  "from": "2026-11-26",
  "to": "2026-11-26",
  "assets": ["019a7f3e-5c21-7a10-8000-0b2c4d6e8f01"],
  "view": { "kind": "trip" },
  "link": "https://reports.eu.navixy.com/reports/trips_and_stops?from=2026-11-26&to=2026-11-26&assets=019a7f3e-5c21-7a10-8000-0b2c4d6e8f01&kind=trip&organization_id=019a7f3e-0a1b-7c2d-8000-4f52474e0001&workspace_id=019a7f3e-0a1b-7c2d-8000-575350430001"
}
```

The link opens the EU address of Reports, with the organization and the workspace of your token.

## What the link opens

* **Default view.** Without `view`, the page opens with its default view.
* **Step that is too short.** When `view.step` gives more than 2016 points for the period, the response raises it to the shortest step that fits. `adjusted` then names the field and says what changed. Any other wrong field returns HTTP 400 with `invalid_tool_call`.
* **Workspace.** The link has the organization and the workspace of your token, so it opens that workspace.
* **Time zone.** The link names no time zone: Reports reads `from` and `to` in the time zone of the browser that opens the link. Send the time zone of the person who opens the link, so that the period and the figures on the page match yours. When `to` is after today in the browser's time zone, the page opens its default period instead.

The request doesn't count toward the daily limit. See [Daily limits](../../limits.md#tools-limits) and [Reports errors](../errors.md#tools-errors).
