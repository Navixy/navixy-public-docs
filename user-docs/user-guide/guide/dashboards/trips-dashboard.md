# Trips dashboard

The **Trips Dashboard** is the second tab of the Dashboards app, and it answers "what did the fleet do over the past week?" (the [Fleet Live Status Dashboard](fleet-live-status-dashboard.md) covers "what is the fleet doing right now?"). It pulls completed trips together into a single view of utilization, distance, duration, and per-object performance. That way, you can spot under-used vehicles, over-worked ones, and unusual driving behavior without building a report.

{% hint style="info" %}
The Trips Dashboard is currently in beta (v1.0.1), an early version released to be shaped by real feedback. If something is missing, confusing, or could be more useful, use the **Send Feedback** button at the top of the tab. The four dashboards version independently, so the Fleet Live Status, Trips, Technical Conditions, and IoT Logic tabs may show different version numbers.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data. Recommended templates to start with: [HM Trip Operations Dashboard](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/04-hm-trip-operations-dashboard.md) and [Trips Dashboard (Yesterday)](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/08-trips-dashboard-yesterday.md).

## What counts as a trip

Every number on this dashboard is built from trips, so it helps to know what the Navixy platform treats as a trip before reading the panels. What follows is a short summary of that same trip-building logic, used elsewhere on the platform too.

The platform processes incoming GPS data every minute and stitches points into trips. Only well-formed points are eligible: a point needs an event ID, valid coordinates, and satellite information. Points that look like GPS jumps (more than 5 km from the previous point) and points that arrive out of order are discarded.

A point counts as moving when the recorded speed is at least 3 km/h, and additionally either the distance from the previous point exceeds 50 m or the device's hardware "is moving" flag is set. A new trip starts after a gap of at least 20 minutes with no data from the device, or when a device starts moving again after being parked for at least 5 minutes. The trip's start time is the timestamp of the first moving point, and the end time is when the final parking segment of the trip began.

For a trip to be recorded at all, four conditions need to hold:

* Total distance is at least 100 m
* Maximum speed reaches at least 3 km/h
* At least 2 GPS points
* Movement was detected

Trips that fail any of these checks are discarded. That's why some very short journeys never show up in the dashboard, and why a vehicle that drifted slightly while parked won't generate a trip.

## Data window and refresh

The Trips Dashboard always shows the last 7 days of trip data. There is no date picker, and the window can't be changed. For trip data over a different period, use the [Trips and parkings report](../reports/specific-report-details/trip-report.md).

The dashboard refreshes automatically every **90 seconds** while the browser tab is in focus, and the **Update** button in the top-right corner forces an immediate refresh.

Because trip processing runs every minute with a small overlap, a trip that ended in the last one or two minutes may not yet be visible, or may appear with an incomplete end time. It will be reflected on the next refresh.

## Fleet activity

The first two KPI tiles frame the week in terms of how much of the fleet was active.

| Tile                   | What it shows                                               |
| ---------------------- | ----------------------------------------------------------- |
| **Total objects**      | Every device registered in your account.                    |
| **Objects with trips** | Devices that recorded at least one trip in the last 7 days. |

The difference between these two is how many devices sat inactive or stored for the week. If it is large, the per-object averages in the sections below will look low for reasons that aren't about driver behavior.

## Trip metrics

Five tiles break down trip volume and distance across the fleet for the week: **Trips count**, **Average trips per object**, **Average distance per trip**, **Total distance**, and **Average distance per object**. Average distance per trip rounds to two decimal places.

{% hint style="info" %}
**Average trips per object** and **Average distance per object** both divide by the total number of registered devices, not by the number that actually had trips. If your account carries many inactive or stored devices, these averages will look lower than your active fleet's picture alone. Use **Objects with trips** and the **Objects with and without trips** donut for the active-only view.
{% endhint %}

## Duration

Two tiles summarize how much time the fleet spent on the move during the week: **Total duration** and **Average duration per trip**. Both are formatted as X h Y min, with seconds truncated rather than rounded.

## Per-object charts

Six charts sit below the fleet-level tiles above. **Objects with and without trips** splits the fleet into active and inactive as a donut. The other five rank the top 10 devices by a single metric: **Top 10 objects by distance**, **Top 10 objects by trips**, **Top 10 objects by speed**, **Top 10 objects by idle time**, and **Top 10 objects by engine hours**.

<details>

<summary>When to read the charts together</summary>

A vehicle that tops the Distance chart but not the Trips chart is likely running long routes rather than short loops. A delivery van doing short loops may top the Trips chart without appearing in the Distance ranking. That contrast often points to different operational patterns worth distinguishing in your analysis.

Idle time and engine hours are most useful side by side. A vehicle that leads in engine hours but not in distance is typically running while stationary, the same idling pattern the idle time chart surfaces. If that pattern is sustained rather than a one-week blip, it's worth checking the same vehicle on the Technical Conditions tab. Prolonged idling tends to show up there as elevated engine RPM hours and board-voltage strain, not just wasted fuel.

The Speed chart may include speeds from trips still in progress when the dashboard loads, so its time coverage can differ slightly from the other charts.

</details>

## Object trip details

The **Object trip details** table at the bottom of the dashboard lists individual trip records, one row per trip. Every column is sortable by clicking its header.

{% hint style="info" %}
The table shows trips longer than 10 km. Shorter trips that still meet the trip-building rules (at least 100 m, 3 km/h, 2 points, movement detected) are counted in **Trips count** and shown in the charts, but don't appear as rows here. Use **Trips count** as your answer to "how many trips happened" and this table as your answer to "where the longer journeys went."
{% endhint %}

The address columns are best-effort. Start and end addresses are resolved by reverse geocoding the coordinates against the Navixy geocoder. When the geocoder is unavailable, address cells appear blank without an error message. Address resolution covers the first 500 rows of the table by default, so rows beyond that limit appear with coordinates but without addresses.

## Exporting panel data

Every panel here, both charts and tables, has the same CSV and PDF export options described in [Exporting data](README.md#exporting-data).
