---
description: >-
  Every panel of Live status, Trips, and Conditions in the Dashboards API: its
  ID, its type, its API row fields, and its tool values with units.
tags:
  - reference
  - beta
---

# Dashboard panels

{% hint style="info" %}
**API and tools**

This page covers both approaches: the rows of [Panel data](panel-data.md) in the API, and the values of [Dashboard data](tools/dashboard-data.md) in the tools.
{% endhint %}

This page lists every panel of the 3 dashboards, with its ID, its type, and what each approach returns for it. Use it to find the panel and the values that your app needs without reading the dashboard definitions. This page is for developers who call the [Dashboards API](README.md).

The 2 last columns compare the approaches:

* **API**: each field of the panel's rows, with its kind in parentheses. The kind gives the unit, as [Rows and values](panel-data.md#rows-and-values) describes. A row of the API has every field.
* **Tools**: each value of the dashboard data, with its unit in parentheses when it has one. "Rows" means that the panel returns `rows`, and "Series" means that it returns `series`, as [What the dashboard data contains](tools/dashboard-data.md#what-the-dashboard-data-contains) describes. The tools leave out `address` and `geofence`, and convert meters to kilometers and seconds to hours.

The panels are in the order of the definition.

## Live status

Dashboard ID: `live_status`. Live status shows the current state of the fleet and has no periods, so a request for it has no `range`. Its speed panels cover the last 24 hours. To learn what each panel counts, see [Live status](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards/live-status) in the Location Intelligence documentation.

<table><thead><tr><th width="50">ID</th><th width="170">Panel</th><th width="100">Type</th><th>API: row fields (kind)</th><th>Tools: values (unit)</th></tr></thead><tbody><tr><td>1</td><td>Total assets</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>2</td><td>Online</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>3</td><td>Offline</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>4</td><td>GPS not updated</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>5</td><td>Other</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>16</td><td>Asset locations</td><td><code>map</code></td><td><code>asset</code> (text), <code>last_updated</code> (time), <code>connection</code> (text), <code>latitude</code> (coordinate), <code>longitude</code> (coordinate)</td><td>Not included</td></tr><tr><td>6</td><td>Connection</td><td><code>piechart</code></td><td><code>status</code> (text), <code>assets</code> (count)</td><td>Rows: <code>status</code>, <code>assets</code></td></tr><tr><td>7</td><td>Top 10 longest unreported</td><td><code>table</code></td><td><code>asset</code> (text), <code>last_updated</code> (time)</td><td>Rows: <code>asset</code>, <code>last_updated</code></td></tr><tr><td>8</td><td>Movement distribution</td><td><code>barchart</code></td><td><code>state</code> (text), <code>assets</code> (count)</td><td>Rows: <code>state</code>, <code>assets</code></td></tr><tr><td>9</td><td>Movement</td><td><code>piechart</code></td><td><code>state</code> (text), <code>assets</code> (count)</td><td>Rows: <code>state</code>, <code>assets</code></td></tr><tr><td>10</td><td>Average and max speed</td><td><code>linechart</code></td><td><code>hour</code> (time), <code>avg_speed</code> (speed), <code>max_speed</code> (speed)</td><td>One series: <code>avg_speed</code> (km/h), <code>max_speed</code> (km/h)</td></tr><tr><td>11</td><td>Top 10 assets by speed</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>max_speed</code> (speed)</td><td>Rows: <code>asset</code>, <code>max_speed</code> (km/h)</td></tr><tr><td>13</td><td>Geofences</td><td><code>table</code></td><td><code>geofence</code> (text), <code>assets</code> (count), <code>asset_list</code> (text)</td><td>Not included</td></tr><tr><td>15</td><td>Fleet details</td><td><code>table</code></td><td><code>asset</code> (text), <code>last_updated</code> (time), <code>offline</code> (text), <code>connection</code> (text), <code>movement</code> (text), <code>speed</code> (speed), <code>latitude</code> (coordinate), <code>longitude</code> (coordinate), <code>address</code> (text), <code>altitude</code> (altitude), <code>satellites</code> (count), <code>hdop</code> (number), <code>heading</code> (heading), <code>geofence</code> (text)</td><td>Rows: <code>asset</code>, <code>last_updated</code>, <code>offline</code>, <code>connection</code>, <code>movement</code>, <code>speed</code> (km/h), <code>latitude</code> (°), <code>longitude</code> (°), <code>altitude</code> (m), <code>satellites</code>, <code>hdop</code>, <code>heading</code> (°)</td></tr></tbody></table>

Notes on the panels:

* **Connection** returns one row for each status: `Online`, `Offline`, `GPS not updated`, and `Other`.
* **Movement distribution** and **Movement** return one row for each state: `Moving`, `Idle`, `Parked`, `Stopped`, and `Unknown`.
* **Asset locations** has `cardOnly: true` in the definition: Navixy Dashboards doesn't show this panel on the dashboard. In the API, its rows are the last known position of each asset, 1,000 assets at most. An asset with no known position has `null` coordinates. The tools don't include it, or **Geofences**: for the positions of the assets, read **Fleet details**.
* **Average and max speed** returns one row for each hour in the API, and one series in the tools.
* **Fleet details** lists the assets updated most recently first, 1,000 at most. The tools return the first 50, and `rowsLeftOut` counts the rest of those 1,000. Its `satellites` is always `null`, and its `address` is always `null` in the API.

API response of **Connection** (`/dashboards/live_status/panels/6`):

```json
{
  "rows": [
    { "status": "Online", "assets": 28 },
    { "status": "Offline", "assets": 1 },
    { "status": "GPS not updated", "assets": 0 },
    { "status": "Other", "assets": 0 }
  ]
}
```

## Trips

Dashboard ID: `trips`. Trips covers a period that you choose in `range`. In the API, a request without `range` covers 14 days, and the tools need a `range`. **Total assets** counts the whole fleet and doesn't depend on the period. To learn how Navixy detects a trip and what each panel counts, see [Trips](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards/trips) in the Location Intelligence documentation.

<table><thead><tr><th width="50">ID</th><th width="170">Panel</th><th width="100">Type</th><th>API: row fields (kind)</th><th>Tools: values (unit)</th></tr></thead><tbody><tr><td>1</td><td>Total assets</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>2</td><td>Assets with trips</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>3</td><td>Trips count</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>4</td><td>Average trips per asset</td><td><code>kpi</code></td><td><code>value</code> (number)</td><td>Rows: <code>value</code></td></tr><tr><td>9</td><td>Average distance per trip</td><td><code>kpi</code></td><td><code>value</code> (distance)</td><td>Rows: <code>value</code> (km)</td></tr><tr><td>5</td><td>Total distance</td><td><code>kpi</code></td><td><code>value</code> (distance)</td><td>Rows: <code>value</code> (km)</td></tr><tr><td>6</td><td>Average distance per asset</td><td><code>kpi</code></td><td><code>value</code> (distance)</td><td>Rows: <code>value</code> (km)</td></tr><tr><td>7</td><td>Total duration</td><td><code>kpi</code></td><td><code>value</code> (duration)</td><td>Rows: <code>value</code> (h)</td></tr><tr><td>8</td><td>Average duration per trip</td><td><code>kpi</code></td><td><code>value</code> (duration)</td><td>Rows: <code>value</code> (h)</td></tr><tr><td>10</td><td>Assets with and without trips</td><td><code>piechart</code></td><td><code>trips</code> (text), <code>assets</code> (count)</td><td>Rows: <code>trips</code>, <code>assets</code></td></tr><tr><td>11</td><td>Top 10 assets by distance</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>distance</code> (distance)</td><td>Rows: <code>asset</code>, <code>distance</code> (km)</td></tr><tr><td>12</td><td>Top 10 assets by trips</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>trips</code> (count)</td><td>Rows: <code>asset</code>, <code>trips</code></td></tr><tr><td>13</td><td>Top 10 assets by speed</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>max_speed</code> (speed)</td><td>Rows: <code>asset</code>, <code>max_speed</code> (km/h)</td></tr><tr><td>14</td><td>Top 10 assets by idle time</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>idle</code> (duration)</td><td>Rows: <code>asset</code>, <code>idle</code> (h)</td></tr><tr><td>15</td><td>Top 10 assets by engine hours</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>engine</code> (duration)</td><td>Rows: <code>asset</code>, <code>engine</code> (h)</td></tr><tr><td>16</td><td>Asset trip details</td><td><code>table</code></td><td><code>asset</code> (text), <code>start</code> (time), <code>end</code> (time), <code>distance</code> (distance), <code>duration</code> (duration), <code>start_latitude</code> (coordinate), <code>start_longitude</code> (coordinate), <code>end_latitude</code> (coordinate), <code>end_longitude</code> (coordinate), <code>points</code> (count)</td><td>Not included</td></tr></tbody></table>

Notes on the panels:

* **Assets with and without trips** returns 2 rows: `With trips` and `Without trips`.
* **Asset trip details** returns the newest 1,000 trips of the period at most in the API. The tools don't include it: the dashboard data has no list of single trips.

API response of **Asset trip details** (`/dashboards/trips/panels/16?range=7d`), shortened to the first 2 rows:

```json
{
  "rows": [
    {
      "asset": "BG-02 Truck (FMC130)",
      "start": "2026-11-27T11:32:59.034Z",
      "end": "2026-11-27T11:52:04.595Z",
      "distance": 8427,
      "duration": 1145,
      "start_latitude": 44.814769,
      "start_longitude": 20.462637,
      "end_latitude": 44.821742,
      "end_longitude": 20.457609,
      "points": 176
    },
    {
      "asset": "PAR-06 Van (FMM150)",
      "start": "2026-11-27T11:32:51.422Z",
      "end": "2026-11-27T11:52:31.057Z",
      "distance": 9906,
      "duration": 1179,
      "start_latitude": 48.862925,
      "start_longitude": 2.295249,
      "end_latitude": 48.853571,
      "end_longitude": 2.369058,
      "points": 147
    }
  ]
}
```

For the rows of **Top 10 assets by distance** in both approaches, see [Panel data](panel-data.md#periods) and [Dashboard data](tools/dashboard-data.md#get-the-dashboard-data).

## Conditions

Dashboard ID: `conditions`. Conditions covers a period that you choose in `range`, as Trips does. **Total assets** counts the whole fleet and doesn't depend on the period. To learn what each panel counts and when a health monitor marks an asset, see [Conditions](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards/conditions) in the Location Intelligence documentation.

<table><thead><tr><th width="50">ID</th><th width="170">Panel</th><th width="100">Type</th><th>API: row fields (kind)</th><th>Tools: values (unit)</th></tr></thead><tbody><tr><td>1</td><td>Total assets</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>2</td><td>Active assets</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>3</td><td>Inactive assets</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>4</td><td>GPS degraded events</td><td><code>kpi</code></td><td><code>value</code> (count)</td><td>Rows: <code>value</code></td></tr><tr><td>5</td><td>SOS events</td><td><code>kpi</code></td><td>Always no rows</td><td>Always <code>unavailable</code></td></tr><tr><td>6</td><td>Seatbelt events</td><td><code>kpi</code></td><td>Always no rows</td><td>Always <code>unavailable</code></td></tr><tr><td>7</td><td>DTC events count</td><td><code>kpi</code></td><td>Always no rows</td><td>Always <code>unavailable</code></td></tr><tr><td>8</td><td>Top 10 assets by GPS degraded events</td><td><code>barchart</code></td><td><code>asset</code> (text), <code>events</code> (count)</td><td>Rows: <code>asset</code>, <code>events</code></td></tr><tr><td>9</td><td>Battery voltage trend for top 10 assets</td><td><code>linechart</code></td><td><code>time</code> (time), <code>asset</code> (text), <code>voltage</code> (voltage)</td><td>Series by <code>asset</code>: <code>voltage</code> (V)</td></tr><tr><td>10</td><td>Top 10 assets by mileage (total odometer)</td><td><code>table</code></td><td><code>asset</code> (text), <code>mileage</code> (distance), <code>time</code> (time)</td><td>Rows: <code>asset</code>, <code>mileage</code> (km), <code>time</code></td></tr><tr><td>11</td><td>Mileage trend for top 10 assets (total odometer)</td><td><code>linechart</code></td><td><code>time</code> (time), <code>asset</code> (text), <code>odometer</code> (distance)</td><td>Series by <code>asset</code>: <code>odometer</code> (km)</td></tr><tr><td>12</td><td>Assets with active fault codes</td><td><code>table</code></td><td>Always no rows</td><td>Always <code>unavailable</code></td></tr><tr><td>13</td><td>Board voltage trend for top 10 assets</td><td><code>linechart</code></td><td><code>time</code> (time), <code>asset</code> (text), <code>voltage</code> (voltage)</td><td>Series by <code>asset</code>: <code>voltage</code> (V)</td></tr><tr><td>14</td><td>GPS health monitor</td><td><code>table</code></td><td><code>asset</code> (text), <code>attention</code> (status), <code>hdop</code> (number), <code>average_hdop</code> (number), <code>hdop_deviation</code> (number), <code>pdop</code> (number), <code>average_pdop</code> (number), <code>pdop_deviation</code> (number), <code>satellites</code> (count), <code>average_satellites</code> (number), <code>satellites_deviation</code> (number)</td><td>Rows: <code>asset</code>, <code>attention</code>, <code>hdop</code>, <code>average_hdop</code>, <code>hdop_deviation</code>, <code>pdop</code>, <code>average_pdop</code>, <code>pdop_deviation</code>, <code>satellites</code>, <code>average_satellites</code>, <code>satellites_deviation</code></td></tr><tr><td>15</td><td>Battery power supply health monitor</td><td><code>table</code></td><td><code>asset</code> (text), <code>attention</code> (status), <code>voltage</code> (voltage), <code>average</code> (voltage), <code>deviation</code> (voltage), <code>change</code> (percent)</td><td>Rows: <code>asset</code>, <code>attention</code>, <code>voltage</code> (V), <code>average</code> (V), <code>deviation</code> (V), <code>change</code> (%)</td></tr><tr><td>16</td><td>Board power supply health monitor</td><td><code>table</code></td><td><code>asset</code> (text), <code>attention</code> (status), <code>voltage</code> (voltage), <code>average</code> (voltage), <code>deviation</code> (voltage), <code>change</code> (percent)</td><td>Rows: <code>asset</code>, <code>attention</code>, <code>voltage</code> (V), <code>average</code> (V), <code>deviation</code> (V), <code>change</code> (%)</td></tr><tr><td>17</td><td>Top 10 events by RPM</td><td><code>table</code></td><td><code>asset</code> (text), <code>rpm</code> (number), <code>time</code> (time)</td><td>Rows: <code>asset</code>, <code>rpm</code>, <code>time</code></td></tr><tr><td>18</td><td>Speed trend for top 10 assets</td><td><code>linechart</code></td><td><code>time</code> (time), <code>asset</code> (text), <code>speed</code> (speed)</td><td>Series by <code>asset</code>: <code>speed</code> (km/h)</td></tr><tr><td>19</td><td>Latest diagnostics</td><td><code>table</code></td><td><code>asset</code> (text), <code>last_update</code> (time), <code>battery_level</code> (percent), <code>battery_voltage</code> (voltage), <code>board_voltage</code> (voltage), <code>gsm_signal_quality</code> (number), <code>roaming</code> (text), <code>hdop</code> (number), <code>pdop</code> (number), <code>satellites</code> (count), <code>mileage</code> (distance), <code>dtc_codes</code> (text), <code>dtc_events</code> (count), <code>rpm</code> (number), <code>max_speed</code> (speed), <code>sos</code> (count), <code>seatbelt</code> (count), <code>max_acceleration</code> (number)</td><td>Rows: <code>asset</code>, <code>last_update</code>, <code>battery_level</code> (%), <code>battery_voltage</code> (V), <code>board_voltage</code> (V), <code>gsm_signal_quality</code>, <code>roaming</code>, <code>hdop</code>, <code>pdop</code>, <code>satellites</code>, <code>mileage</code> (km), <code>dtc_codes</code>, <code>dtc_events</code>, <code>rpm</code>, <code>max_speed</code> (km/h), <code>sos</code>, <code>seatbelt</code>, <code>max_acceleration</code></td></tr></tbody></table>

Notes on the panels:

* Panels 5, 6, 7, and 12 always have no data: **SOS events**, **Seatbelt events**, **DTC events count**, and **Assets with active fault codes**. The API returns an empty `rows` list, and the tools return the panel's empty message in `unavailable`.
* The line charts of the top 10 assets return one row for each asset and time step in the API. The tools return one series for each asset. The step is 5 minutes over a period in hours and 1 hour over a period in days.
* In the 3 health monitors, `attention` is `critical`, `warning`, or `normal`. **GPS health monitor** also returns `no_fix` for a GPS device without a position.
* The 3 health monitors and **Latest diagnostics** return 1,000 rows at most, the ones that need attention most first. The tools return the first 50, and `rowsLeftOut` counts the rest of those 1,000.
  * **GPS health monitor** lists `no_fix` first, then `critical`, `warning`, and `normal`, and within each the highest HDOP first.
  * **Battery power supply health monitor** and **Board power supply health monitor** list the largest drop of `change` first.
  * **Latest diagnostics** lists the latest update first.
* In **GPS health monitor** and **Latest diagnostics**, `satellites` is the fewest satellites that the GPS fix type needs. It isn't a count of the satellites in view. It's `null` when the GPS device reports no fix type.
* **Top 10 events by RPM** returns the 10 hours with the highest RPM in the period, each with its asset. So one asset can appear in several rows.
* **Latest diagnostics** returns `roaming` as `Home`, `Roaming`, or `null`. Its `dtc_codes`, `dtc_events`, `sos`, `seatbelt`, and `max_acceleration` are always `null`.

API response of **Battery voltage trend for top 10 assets** (`/dashboards/conditions/panels/9?range=24h`), shortened to the first 3 rows. Over 24 hours, the step is 5 minutes, so the full response has up to 288 rows for each asset:

```json
{
  "rows": [
    { "time": "2026-11-26T14:30:00Z", "asset": "BG-01 Van (FMB920)", "voltage": 4.05 },
    { "time": "2026-11-26T14:30:00Z", "asset": "BG-02 Truck (FMC130)", "voltage": 4.05 },
    { "time": "2026-11-26T14:30:00Z", "asset": "BG-03 Bus (FMB125)", "voltage": 4.05 }
  ]
}
```

API response of **GPS health monitor** (`/dashboards/conditions/panels/14?range=7d`), shortened to the first row. The satellite fields are `null` because this GPS device reports no fix type:

```json
{
  "rows": [
    {
      "asset": "BG-04 Truck (FMB140)",
      "attention": "normal",
      "hdop": 0.8,
      "average_hdop": 0.7420052473026618,
      "hdop_deviation": 0.057994764618267136,
      "pdop": 1.1,
      "average_pdop": 0.9840104946053237,
      "pdop_deviation": 0.11598952923653416,
      "satellites": null,
      "average_satellites": null,
      "satellites_deviation": null
    }
  ]
}
```
