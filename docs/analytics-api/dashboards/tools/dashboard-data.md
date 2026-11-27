---
description: >-
  Read the dashboards and periods that a token can use, then what every panel
  of a dashboard shows for a period: units, rows, lines, and periods.
tags:
  - fundamentals
  - beta
---

# Dashboard data

{% hint style="info" %}
**Tools, with an API key**

The operations on this page are tools. They accept the access token that your app gets by exchanging a Navixy Console API key. See [Tools access](../../authentication.md#tools-access).
{% endhint %}

Dashboard data is what every panel of one dashboard shows for a period, in one response. Your app first lists the dashboards and periods that the token can read. Then it reads the data of a dashboard with its code, a period, and your time zone. For example, an app can read Trips over the last 7 days and copy the distance of each asset to a spreadsheet. This page is for developers of integrations and AI agents who call the [Dashboards tools](README.md) with an access token from [Authentication](../../authentication.md).

The 2 tool operations, [List dashboards and periods](README.md) and [Get dashboard data](README.md), are on the Dashboards tools page. To learn what each panel returns, see [Dashboard panels](../dashboard-panels.md).

{% hint style="info" %}
**Access**

The role of the API key must include Dashboards and its `dashboards-standard` feature, and grant the dashboard. The data includes only the assets, GPS devices, and geo objects that the role lets you see. See [Access](../README.md#access).
{% endhint %}

## List the dashboards and periods

First, list the dashboards and periods that the token can read, with [List dashboards and periods](README.md#list-the-dashboards-and-periods). The response gives the dashboard codes and the period IDs that a request for dashboard data accepts. It reads no panel data, so it doesn't count toward the daily limit.

## Get the dashboard data

The following request gets the data of Live status:

```bash
curl --request POST \
  --url 'https://dashboards.<REGION>.navixy.com/api/tools/read_dashboard' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Berlin", "dashboard": "live_status"}'
```

Parameters:

* `timeZone`: `Europe/Berlin` writes every time of the response in the time zone of Berlin. See [Time zone](../../README.md#time-zone).
* `dashboard`: `live_status` is the code of the Live status dashboard, from the `dashboards` list.
* No `range`: Live status has no periods. Trips and Conditions need one, such as `"range": "7d"` for the last 7 days.

Response from a workspace without assets, shortened to 4 panels:

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
      "fields": [
        {
          "key": "value"
        }
      ],
      "rows": [
        {
          "value": 0
        }
      ]
    },
    {
      "id": 16,
      "type": "map",
      "title": "Asset locations",
      "unavailable": "each asset's last position on a map, whose coordinates Fleet details answers"
    },
    {
      "id": 6,
      "type": "piechart",
      "title": "Connection",
      "description": "Breakdown of the fleet by current connection status.\nOnline: Status is online and a message was received within the last 6 minutes.\nOffline: The last message is older than 2 days or missing entirely.\nGPS not updated: Message received less than 6 minutes ago, but online conditions are not met.\nOther: The last message was received between 6 minutes and 2 days ago.",
      "fields": [
        {
          "key": "status"
        },
        {
          "key": "assets",
          "label": "Assets"
        }
      ],
      "rows": []
    },
    {
      "id": 10,
      "type": "linechart",
      "title": "Average and max speed",
      "description": "Average and maximum speed over the last 24 hours.",
      "fields": [
        {
          "key": "avg_speed",
          "label": "Average speed",
          "unit": "km/h"
        },
        {
          "key": "max_speed",
          "label": "Max speed",
          "unit": "km/h"
        }
      ],
      "series": []
    }
  ]
}
```

In a workspace with assets, the same panels return values. For example, **Top 10 assets by distance**, panel 11 of Trips over the last 7 days, shortened to the first 3 rows:

```json
{
  "id": 11,
  "type": "barchart",
  "title": "Top 10 assets by distance",
  "description": "Assets ranked by total distance traveled over the selected period.",
  "fields": [
    { "key": "asset" },
    { "key": "distance", "label": "Distance", "unit": "km" }
  ],
  "rows": [
    { "asset": "NV4-150 · FMC150 (D)", "distance": 12039.68 },
    { "asset": "NV4-300 - FMC130 (D)", "distance": 8801.83 },
    { "asset": "IST-02 Truck (FMM250)", "distance": 8732.56 }
  ]
}
```

## What the dashboard data contains

The dashboard data has the dashboard's code and name, the period, and every panel of the dashboard in the order of the dashboard. `from` and `to` give the period in your time zone, and both are `null` on Live status. Each panel has its `id`, `type`, `title`, and `description`, then one of 3 forms:

<table><thead><tr><th width="180">Form</th><th>What the panel returns</th></tr></thead><tbody><tr><td>Rows</td><td><code>fields</code> and <code>rows</code>. Number panels, pie charts, bar charts, and tables return their rows, 50 at most. When a table has more, <code>rowsLeftOut</code> counts the rows that the dashboard data leaves out. A table reads 1,000 rows at most, so <code>rowsLeftOut</code> is 950 at most. Which rows come first depends on the panel: see <a href="../dashboard-panels.md">Dashboard panels</a>.</td></tr><tr><td>Series</td><td><code>fields</code> and <code>series</code>. A line chart returns one series for each line: a summary of each value and the line's points. See <a href="#line-charts">Line charts</a>.</td></tr><tr><td>Unavailable</td><td><code>unavailable</code> only: the text says why the panel has no data. The dashboard data doesn't include the panel, the panel has no data source, or its data couldn't be read. The other panels still return their data.</td></tr></tbody></table>

### Units

Each field has a `unit` when its values have one. The dashboard data has the values in these units and rounds each value as follows:

<table><thead><tr><th width="100">Unit</th><th>Values</th></tr></thead><tbody><tr><td><code>km</code></td><td>Distance in kilometers, to 2 decimals</td></tr><tr><td><code>h</code></td><td>Duration in hours, as a decimal number to 2 decimals: <code>1.5</code> is 1 hour 30 minutes</td></tr><tr><td><code>km/h</code></td><td>Speed in kilometers per hour, to 1 decimal</td></tr><tr><td><code>V</code></td><td>Voltage in volts, to 2 decimals</td></tr><tr><td><code>%</code></td><td>Percent, to 1 decimal, where 100 is the whole</td></tr><tr><td><code>m</code></td><td>Altitude in whole meters above sea level</td></tr><tr><td><code>°</code></td><td>A latitude or a longitude in degrees, to 5 decimals, or a heading in degrees clockwise from north</td></tr></tbody></table>

A field without a unit is text, a time, a count, or a plain number. A plain number is rounded to 2 decimals. A time is RFC 3339 text in the time zone of the request, as [Time zone](../../README.md#time-zone) describes. A value is `null` when the row has no value for the field.

### Line charts

A line chart returns `series`: one item for each line, such as one for each asset. The item names the line in the key of the panel's first text field, such as `asset`. `points` is the number of rows that the line has in the period. `times` gives the time of each point that the dashboard data returns. Each value field has `first`, `last`, `min`, `max`, `mean`, and `values`, the value at each of `times`. The mean is rounded to the decimals of the unit, and to 2 decimals at most.

A line returns 48 points at most. Over a period with more steps, each point is the average of a part of the period, at the start of the part. A part is 30 minutes over 24 hours and 7 hours over 14 days. A line has a point only for the parts where it has data. Over a short period, such as `1h`, it has a point for each of its rows.

So 2 lines can have `times` of different lengths. To compare 2 lines, match their points by the value in `times`, not by position. A `null` in `values` means that the line has data at that time but no value for that field.

A chart with one line, such as **Average and max speed** on Live status, returns its series without a name key. A chart without data returns an empty list, as `"series": []` in the Live status response above shows.

## Periods and the previous period

On Trips and Conditions, `range` chooses the period: `1h`, `6h`, `12h`, `24h`, `7d`, `14d`, or `31d`. Each period ends at the moment of the request.

To compare 2 periods, read the dashboard a second time with `"previous": true`. The data then covers the period of the same length right before: with `"range": "7d"`, the 7 days before the last 7 days. `previous` is a JSON boolean, and it needs `range`.

Each request for dashboard data counts toward a daily limit. A request can take several seconds, because Navixy queries each panel in turn. See [Tools limits](../../limits.md#tools-limits).

## See also

* [Dashboards tools](README.md): The 2 tool operations and their response models.
* [Dashboard panels](../dashboard-panels.md): What each panel of Live status, Trips, and Conditions returns.
* [Error handling](../../error-handling.md#common-error-codes): The error codes that the tools of both products return.
