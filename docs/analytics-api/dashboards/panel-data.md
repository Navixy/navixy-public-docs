---
description: >-
  Dashboards API operation: read the rows of one dashboard panel for a period, and the
  units of the values in each row.
tags:
  - fundamentals
  - beta
---

# Panel data

{% hint style="info" %}
**API, with a session token**

The operation on this page is an API operation. It accepts the session token of a person signed in to Navixy Dashboards in a browser. See [API access](../authentication.md#api-access).
{% endhint %}

Panel data is the rows of one dashboard panel for a period, such as the top 10 assets by distance over 7 days. Navixy Dashboards reads each panel this way. This page is for developers who read panels in a signed-in session or in a script of the signed-in person. For a panel's ID and its fields, see [Dashboard panels](dashboard-panels.md) or the [dashboard's definition](dashboard-definitions.md#read-the-definition-of-a-dashboard).

## Rows and values

Each row is a JSON object with one key for each of the panel's fields, in the order of the fields. A value is a number, text, or `null` when the row has no value for the field. For example, an average is `null` when there is nothing to average.

The `kind` of a field gives its unit. Your app converts and rounds the values for display:

<table><thead><tr><th width="140">Kind</th><th>Value</th></tr></thead><tbody><tr><td><code>text</code></td><td>Text, such as an asset label or a category name</td></tr><tr><td><code>time</code></td><td>A date and time in UTC, as <a href="https://www.rfc-editor.org/rfc/rfc3339">RFC 3339</a> text</td></tr><tr><td><code>count</code></td><td>A whole number</td></tr><tr><td><code>number</code></td><td>A number</td></tr><tr><td><code>distance</code></td><td>Meters</td></tr><tr><td><code>duration</code></td><td>Seconds</td></tr><tr><td><code>speed</code></td><td>Kilometers per hour</td></tr><tr><td><code>voltage</code></td><td>Volts</td></tr><tr><td><code>percent</code></td><td>Percent, where 100 is the whole</td></tr><tr><td><code>altitude</code></td><td>Meters above sea level</td></tr><tr><td><code>coordinate</code></td><td>A latitude or a longitude, in degrees</td></tr><tr><td><code>heading</code></td><td>Degrees clockwise from north</td></tr><tr><td><code>status</code></td><td>The result of a health check: <code>critical</code>, <code>warning</code>, <code>normal</code>, or <code>no_fix</code> when the GPS has no position</td></tr></tbody></table>

A field with `signed: true` is a difference, such as a current voltage minus its average, so its value can be negative.

The shape of the rows depends on the panel's `type`:

* `kpi`: one row with the key `value`.
* `piechart` and `barchart`: one row for each category or asset.
* `linechart`: one row for each time step. A chart of several assets has one row for each asset and each time step, with the asset in the `asset` field. The step is the panel's `stepSeconds` when it has one, such as 1 hour on Live status. Otherwise, it's the period's: 5 minutes over a period in hours, and 1 hour over a period in days.
* `table` and `map`: one row for each asset, trip, or geofence.

An empty `rows` list means that the panel has no data for the workspace and period. The panel's `emptyMessage` in the definition is the text that Navixy Dashboards shows then. Some panels always return an empty list, and some fields are always `null`: see [Dashboard panels](dashboard-panels.md).

## Periods

On Trips and Conditions, the `range` parameter chooses the period: `1h`, `6h`, `12h`, `24h`, `7d`, `14d`, or `31d`. Each period ends at the moment of the request. Without `range`, the panel covers `14d`, the `defaultRange` of both dashboards. **Total assets** counts the whole fleet and doesn't depend on the period.

The following request reads **Top 10 assets by distance**, panel 11 of Trips, over the last 7 days:

```bash
curl --request GET \
  --url 'https://dashboards.<REGION>.navixy.com/api/dashboards/trips/panels/11?range=7d' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response, shortened to the first 3 rows:

```json
{
  "rows": [
    { "asset": "NV4-150 · FMC150 (D)", "distance": 12039682 },
    { "asset": "NV4-300 - FMC130 (D)", "distance": 8801829 },
    { "asset": "IST-02 Truck (FMM250)", "distance": 8732564 }
  ]
}
```

The `distance` field has the kind `distance`, so the values are in meters.

To compare 2 periods, read the panel a second time with `previous=true`. The rows then cover the period of the same length right before: with `range=7d&previous=true`, the 7 days before the last 7 days. `previous` needs `range`, because the request must name the period to go back from.

Live status has no periods. A Live status panel with `range` returns HTTP 400 with `range_not_found`.

The periods of the API don't depend on a time zone. Each one ends at the moment of the request, and every `time` value is in UTC. The operation is [Read panel data](api-reference.md).

## Limits

A table panel returns 1,000 rows at most, and a ranking 10. A panel whose query is too slow or reads too much data returns HTTP 503 with `panel_data_unavailable`. It can also return an HTTP 5xx error without a JSON body. Retry later, or read a shorter period. See [API limits](../limits.md#api-limits).

## Access

The operation needs the same access as [reading the dashboard's definition](dashboard-definitions.md#access). The rows include only the assets, GPS devices, and geo objects that the role of the signed-in person lets them see. The API checks access before it checks the panel ID, `range`, and `previous`, so a session without access gets HTTP 403 whatever the request has. For the error codes, see [API errors](errors.md#api-errors).

## See also

* [Dashboard definitions](dashboard-definitions.md): List the dashboards and read their definitions.
* [Dashboard panels](dashboard-panels.md): Every panel of the 3 dashboards, with the fields of its rows.
* [Dashboards API reference](api-reference.md): The API operations and their response models.
