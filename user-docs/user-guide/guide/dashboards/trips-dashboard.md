# Trips dashboard

The **Trips Dashboard** is the second tab of the Dashboard app. Where the [Fleet Live Status Dashboard](fleet-live-status-dashboard.md) answers "what is the fleet doing right now?", the Trips Dashboard answers "what did the fleet do over the past week?". It pulls completed trips together into a single view of utilization, distance, duration, and per-object performance, so you can spot under-used vehicles, over-worked ones, and unusual driving behavior without building a report.

{% hint style="info" %}
The Trips Dashboard is currently in beta (v1.0.0). It's an early version released so we can shape it based on real feedback. If something is missing, confusing, or could be more useful, please tell us through the **Send Feedback** button at the top of the tab. The three dashboards version independently, so the Fleet Live Status, Trips, and Technical Conditions tabs may show different version numbers.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data. Recommended templates to start with: [Trip Operations Dashboard](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/schemas/04-hm-trip-operations-dashboard.md) and [Trips Dashboard (Yesterday)](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/schemas/08-trips-dashboard-yesterday.md).

## What counts as a trip

Every number on this dashboard is built from trips, so it helps to know what the platform treats as a trip before reading the panels. The same trip-building logic is used elsewhere in the platform; this section is a short summary so the dashboard makes sense in context.

The platform processes incoming GPS data every minute and stitches points into trips. Only well-formed points are eligible: a point needs an event ID, valid coordinates, and satellite information. Points that look like GPS jumps (more than 5 km from the previous point) and points that arrive out of order are discarded.

A point counts as moving when the recorded speed is at least 3 km/h, and additionally either the distance from the previous point exceeds 50 m or the device's hardware "is moving" flag is set. A new trip starts after a gap of at least 20 minutes with no data from the device, or when a device starts moving again after being parked for at least 5 minutes. The trip's start time is the timestamp of the first moving point, and the end time is when the final parking segment of the trip began.

For a trip to be recorded at all, four conditions need to hold. Trips that fail any of these checks are discarded, which is why some very short journeys never show up in the dashboard, and why a vehicle that drifted slightly while parked won't generate a trip.

* Total distance is at least 100 m
* Maximum speed reaches at least 3 km/h
* At least 2 GPS points
* Movement was detected

## Data window and refresh

The Trips Dashboard always shows the last 7 days of trip data. There is no date picker, and the window cannot be changed. For trip data over a different period, use the [Trips and parkings report](../reports/specific-report-details/trip-report.md).

The dashboard refreshes itself every **90 seconds** while the browser tab is in focus, and the **Update** button in the top-right corner forces an immediate refresh. The 90-second cadence is slower than the Fleet Live Status tab's 60 seconds, because the trip-level aggregations span a week of data and are more expensive to compute.

Because trip processing runs every minute with a small overlap, a trip that ended in the last one or two minutes may not yet be visible, or may appear with an incomplete end time. It will be reflected on the next refresh.

## Fleet activity

The first two KPI tiles frame the week in terms of how much of the fleet was active.

| Tile                   | What it shows                                               |
| ---------------------- | ----------------------------------------------------------- |
| **Total Objects**      | Every device registered in your account.                    |
| **Objects with Trips** | Devices that recorded at least one trip in the last 7 days. |

The gap between these two is your share of inactive or stored devices for the week. If it is large, the per-object averages in the sections below will look low for reasons that aren't about driver behavior.

## Trip metrics

Five tiles break down trip volume and distance across the fleet for the week.

| Tile                         | What it shows                                                     |
| ---------------------------- | ----------------------------------------------------------------- |
| **Amount of Trips**          | Total count of trips recorded across the fleet.                   |
| **Trips per Object, AVG**    | Average number of trips per registered device.                    |
| **Distance per Trip, AVG**   | Average trip length in kilometers, rounded to two decimal places. |
| **Total Distance**           | Sum of all trip distances over the period.                        |
| **Distance per Object, AVG** | Average kilometers per registered device.                         |

{% hint style="info" %}
**Trips per Object, AVG** and **Distance per Object, AVG** both divide by the total number of registered devices, not by the number that actually had trips. If your account carries many inactive or stored devices, these averages will look lower than your active fleet's picture alone. Use **Objects with Trips** and the **Objects with/without Trips** donut for the active-only view.
{% endhint %}

## Duration

Two tiles summarize how much time the fleet spent on the move during the week.

| Tile                       | What it shows                                                                   |
| -------------------------- | ------------------------------------------------------------------------------- |
| **Total Duration**         | Sum of all trip durations across the fleet.                                     |
| **Duration per Trip, AVG** | Average trip duration, formatted as X h Y min (seconds truncated, not rounded). |

## Per-object charts

Six charts break the same week of data down by individual object. Each ranks the top 10 devices by a specific metric, giving a per-vehicle picture to complement the fleet-level tiles above.

| Panel                                          | What it shows                                                                                          |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Objects with/without Trips** (donut chart)   | The fleet split between devices that recorded at least one trip during the week and those that didn't. |
| **Top 10 Objects by Distance** (bar chart)     | Vehicles ranked by total kilometers traveled.                                                          |
| **Top 10 Objects by Trips** (bar chart)        | Vehicles ranked by number of trips.                                                                    |
| **Top 10 Objects by Speed** (bar chart)        | Devices ranked by highest maximum speed recorded during the week.                                      |
| **Top 10 Objects by Idling Time** (bar chart)  | Devices ranked by time spent idling.                                                                   |
| **Top 10 Objects by Engine Hours** (bar chart) | Devices ranked by total engine-on time.                                                                |

<details>

<summary>When to read the charts together</summary>

A vehicle that tops the Distance chart but not the Trips chart is likely running long routes rather than short loops. A delivery van doing short loops may top the Trips chart without appearing in the Distance ranking. That contrast often points to different operational patterns worth distinguishing in your analysis.

Idling Time and Engine Hours are most useful side by side. A vehicle that leads in engine hours but not in distance is typically running while stationary, which traces back to the same idling pattern the Idling Time chart surfaces.

The Speed chart may include speeds from trips still in progress when the dashboard loads, so its time coverage can differ slightly from the other charts.

</details>

## Objects trips details

The **Objects Trips Details** table at the bottom of the dashboard lists individual trip records, one row per trip. Every column is sortable by clicking its header, and the table scrolls horizontally on narrower screens. Columns include object label, start and end times, distance and duration, start and end coordinates, start and end addresses, and the number of GPS points in the trip.

{% hint style="info" %}
The table shows trips longer than 10 km. Shorter trips that still meet the trip-building rules (at least 100 m, 3 km/h, 2 points, movement detected) are counted in **Amount of Trips** and shown in the charts, but do not appear as rows here. Use the KPI tile as your answer to "how many trips happened" and the table as your answer to "where the longer journeys went".
{% endhint %}

The address columns are best-effort. Start and end addresses are resolved by reverse geocoding the coordinates against the Navixy geocoder. When the geocoder is unavailable, address cells appear blank without an error message. Address resolution covers the first 500 rows of the table by default, so rows beyond that limit appear with coordinates but without addresses.

## Exporting panel data

Every panel on the Trips Dashboard, both charts and tables, has a download icon in its top-right corner. Click it to export the panel's current data as a CSV file. The export reflects the state of the panel at the moment you click, which is useful for capturing a snapshot for a handover, a weekly report, or further analysis in a spreadsheet.

Like the other Dashboard tabs, the Trips Dashboard does not currently support global filters, custom time ranges, or shared links.
