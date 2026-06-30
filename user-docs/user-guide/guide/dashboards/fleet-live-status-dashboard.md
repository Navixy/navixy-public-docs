# Fleet live status dashboard

The **Fleet Live Status Dashboard** is the first tab of the Dashboard app. Where the **Trips Dashboard** answers "what did the fleet do over the past week?", the Fleet Live Status Dashboard answers "what is the fleet doing right now?". It brings connection status, movement state, speed, and location data together into a single view, so you can assess your fleet's current situation without switching between the Objects list, the map, and individual reports.

{% hint style="info" %}
The Fleet Live Status Dashboard is currently in beta (v1.0.1). It's an early version released so we can shape it based on real feedback. If something is missing, confusing, or could be more useful, please tell us through the **Send Feedback** button at the top of the tab. The two dashboards version independently, so the Fleet Live Status and Trips tabs may show different version numbers.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data. Recommended templates to start with: [Object Status Dashboard](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/schemas/07-object-status-dashboard.md) and [Fleet Reports Dashboard](https://github.com/SquareGPS/navixy-iot-query-dashboard/blob/main/schemas/03-fleet-reports-dashboard.md).

## Movement states

The dashboard uses four movement states to classify what each object is doing. These states appear in the Movement chart and in the Fleet Details table, color-coded as shown below.

| State      | Color  | Condition                                    |
| ---------- | ------ | -------------------------------------------- |
| 🟢 Moving  | Green  | Speed above 2 km/h                           |
| 🟣 Stopped | Violet | No message received for more than 10 minutes |
| 🟠 Parked  | Orange | Stationary with engine off                   |
| 🔵 Idling  | Blue   | Stationary with engine on, speed at 0 km/h   |

## Connection states

The dashboard uses the same four connection states as the rest of the platform. A concise reference is provided below; for full definitions including the state transition sequence, see [Connection state](../tracking/objects-list/connection-state.md).

| State              | Condition                                                                                               |
| ------------------ | ------------------------------------------------------------------------------------------------------- |
| 🟢 Online          | Device is connected and reporting GPS coordinates as expected.                                          |
| 🟡 GPS not updated | Device is connected to the server but has not transmitted valid GPS coordinates for at least 5 minutes. |
| 🔴 Offline         | Device has not sent any data for longer than the configured timeout (default: 10 minutes).              |
| ⬜ Other            | Devices not yet activated, suspended, or in another non-standard state.                                 |

## Data window and refresh

The Fleet Live Status Dashboard shows the current state of your fleet, not a historical window. Connection and movement states are computed as new telemetry arrives, typically within seconds of a device reporting.

The dashboard refreshes automatically every **60 seconds** while the browser tab is in focus. When you switch away from the tab, updates pause and resume when you return. Use the **Update** button in the top-right corner to force an immediate refresh at any time.

Geofence and Points of Interest membership updates within a few minutes of an object entering or leaving a zone, slightly slower than connection and movement states because zone calculations check each object's position against all defined polygons.

## Connection status

The top of the dashboard answers the most immediate operational question: how many devices are working right now? These panels give you the count and the distribution at a glance.

| Panel                           | What it shows                                                                                                                                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Total Objects** _(KPI)_       | Every device registered in your account.                                                                                                                                                                |
| **Online** _(KPI)_              | Devices currently connected and reporting GPS as expected.                                                                                                                                              |
| **Offline** _(KPI)_             | Devices that have not sent data for longer than the configured timeout.                                                                                                                                 |
| **GPS not updated** _(KPI)_     | Devices connected to the server but not transmitting valid GPS coordinates.                                                                                                                             |
| **Other** _(KPI)_               | Devices in any other state, including suspended or not yet activated.                                                                                                                                   |
| **Connection** _(donut chart)_  | The same five counts shown as proportions, making it easier to see whether a significant share of the fleet is offline.                                                                                 |
| **Top 5 long-unseen** _(table)_ | The five objects with the oldest Last Updated timestamps, sorted from the longest gap to the shortest. Surfaces the devices most likely to need attention before drivers or customers report a problem. |

<details>

<summary>When to investigate using Top 5 long-unseen</summary>

Devices appearing here have gone the longest without contacting the platform. Common causes include dead or disconnected batteries, vehicles parked in areas with poor cellular signal (underground parking, remote sites), exhausted SIM data limits, and devices that have been physically moved or decommissioned without being removed from the account. Checking this table at the start of a shift is faster than sorting the Objects list by Last Updated.

</details>

## Movement

The movement section tells you what your fleet is actually doing, not just whether devices are reporting. Both panels draw from the same data; they complement each other rather than duplicate.

| Panel                                   | What it shows                                                                                                             |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Movement** _(donut chart)_            | The fleet split across the four movement states as proportions.                                                           |
| **Movement Distribution** _(bar chart)_ | The same four movement states as absolute counts, making it easier to compare volumes when one state dominates the donut. |

<details>

<summary>Reading the movement distribution</summary>

The balance of states shifts predictably through the day. A fleet showing most objects as Parked early in the morning is normal before a shift starts; the same picture at midday suggests routes are not running on schedule. A high Idling count at any time of day is worth noting: idling while stationary with the engine on accumulates fuel cost and engine wear that doesn't appear in trip distance or duration figures.

</details>

## Speed

Two panels cover speed from different angles. The chart shows fleet-wide patterns over time; the table identifies the individual objects responsible for peaks.

| Panel                                    | What it shows                                                                                                                                            |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Average and max speed** _(line chart)_ | Fleet-wide average speed and the single highest speed recorded across all objects, plotted over the last 24 hours.                                       |
| **Top 10 objects by speed** _(table)_    | Individual objects ranked by their highest recorded speed, making it straightforward to identify which vehicles produced the peaks visible in the chart. |

## Geofences

The Geofences panel shows where objects are right now relative to the zones configured in your account.

| Panel                   | What it shows                                                                                                                                           |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Geofences** _(table)_ | Zones that currently contain at least one object, with the count and labels of those objects. Sorted by object count from highest to lowest by default. |

{% hint style="info" %}
This panel lists only zones that currently contain objects. Empty zones do not appear. For the complete list of configured zones, use [Map tools → Geofences](../tracking/map-tools/geofences.md).
{% endhint %}

## Points of interest

The Points of Interest panel is the companion to Geofences for accounts organized around specific stops rather than larger zones. A point of interest is a single location with a radius: a customer site, a pickup point, a fuel station, or a checkpoint.

| Panel                            | What it shows                                                                                                                                                                                                                                 |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Points of Interest** _(table)_ | POIs that currently have at least one object inside their radius, with the object count and an alphabetical list of object labels. Objects without a label set appear as `No Data`. Sorted by object count from highest to lowest by default. |

{% hint style="info" %}
The panel shows up to 100 POIs at a time, ranked by object count. POIs with no objects nearby do not appear. The panel updates within a few minutes of an object entering or leaving a POI radius.
{% endhint %}

## Fleet details

The **Fleet Details** table is the detail layer beneath all the summary panels above. It lists every object in your account with its current state across all the dimensions the dashboard covers, plus position and address information.

Columns include the object label, last update timestamp, connection status, movement status, current speed, altitude, satellite count, HDOP (Horizontal Dilution of Precision, a measure of GPS fix quality), heading, current geofence membership, coordinates, and resolved address. Every column is sortable by clicking its header.

{% hint style="info" %}
The table displays a maximum of 500 rows. If your account contains more than 500 objects, only the first 500 appear based on the current sort order.
{% endhint %}

<details>

<summary>How to use Fleet Details during an investigation</summary>

Sorting by **Last Updated** brings the most recently active objects to the top, which is useful when verifying that a specific vehicle has checked in. Sorting by **Speed** surfaces the fastest objects at that moment. Sorting by **Address** groups objects by their current location, which can help confirm that vehicles have reached expected sites. For objects flagged in the Top 5 long-unseen table, searching by object label in Fleet Details shows their full current state alongside their last known coordinates.

A low satellite count or a high HDOP value alongside an unexpected location often indicates poor GPS signal rather than an actual position: the device is reporting, but the fix is unreliable. In open-sky conditions, HDOP values below 2 are typical; values above 5 suggest the position should be treated with caution.

</details>

## Exporting panel data

Every panel on the Fleet Live Status Dashboard, both charts and tables, has a download icon in its top-right corner. Click it to export that panel's current data as a CSV file. The export reflects the state of the panel at the moment you click, which is useful for capturing a snapshot for a handover, an email, or further analysis in a spreadsheet.

The dashboard does not currently support global filters, custom time ranges, or shared links.
