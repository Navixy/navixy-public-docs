# Fleet live status dashboard

The **Fleet Live Status Dashboard** is the first tab of the Dashboards app, and it answers "what is the fleet doing right now?" (the [Trips Dashboard](trips-dashboard.md) covers "what did the fleet do over the past week?"). It brings connection status, movement state, speed, and location data together into a single view. That way, you can assess your fleet's current situation without switching between the Objects list, the map, and individual reports.

{% hint style="info" %}
The Fleet Live Status Dashboard is currently in beta (v1.0.3), an early version released to be shaped by real feedback. If something is missing, confusing, or could be more useful, use the **Send Feedback** button at the top of the tab. The four dashboards version independently, so the Fleet Live Status, Trips, Technical Conditions, and IoT Logic tabs may show different version numbers.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data. Recommended templates to start with: [Object Status Dashboard](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/07-object-status-dashboard.md) and [Fleet Reports Dashboard](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/03-fleet-reports-dashboard.md).

## Movement states

The dashboard uses five movement states to classify what each object is doing. These states appear in the Movement chart and in the Fleet details table, color-coded as shown below.

| State      | Color  | Condition                                    |
| ---------- | ------ | -------------------------------------------- |
| 🟢 Moving  | Green  | Speed above 2 km/h                           |
| 🟣 Stopped | Violet | No message received for more than 10 minutes |
| 🟠 Parked  | Orange | Device is stationary                         |
| 🔵 Idling  | Blue   | Engine is on, speed is 0 km/h                |
| ⚪ Unknown | Gray   | No data available                            |

<details>

<summary>Why Stopped and Offline can disagree</summary>

Stopped and Offline both come from a device going quiet, but on different clocks: Stopped fires after 10 minutes of silence, Offline only after 2 days. An object can sit in Stopped on the Movement chart while still showing Other, not yet Offline, on the Connection chart. That overlap is normal, not a contradiction, since the two charts use different timeouts for the same missing data. A rising Stopped count is a reporting problem to check, not a sign the fleet has actually stopped working. Unknown, by contrast, usually means a device that has never reported at all rather than one that stopped reporting.

</details>

## Connection states

The dashboard uses four connection states, defined below. For how connection status works in the [Objects list](../tracking/objects-list/), see [Connection state](../tracking/objects-list/connection-state.md); its thresholds are tracked separately from the ones below.

| State              | Condition                                                                                    |
| ------------------ | --------------------------------------------------------------------------------------------- |
| 🟢 Online          | The device's status is online, and it sent a message within the last 6 minutes.               |
| 🟡 GPS not updated | The device sent a message within the last 6 minutes, but the online conditions above aren't met. |
| 🔴 Offline         | The last message is older than 2 days, or none has been received.                             |
| ⬜ Other            | The last message was received between 15 minutes and 2 days ago.                              |

## Data window and refresh

The Fleet Live Status Dashboard shows the current state of your fleet rather than a historical window, with one exception: the Speed panels plot a rolling 24-hour trend. Connection and movement states are computed as new telemetry arrives, typically within seconds of a device reporting.

The dashboard refreshes automatically every **90 seconds** while the browser tab is in focus. When you switch away from the tab, updates pause, and they resume when you return. Use the **Update** button in the top-right corner to force an immediate refresh at any time.

Geofence and Points of Interest membership updates within a few minutes of an object entering or leaving a zone, slightly slower than connection and movement states because zone calculations check each object's position against all defined polygons.

## Connection status

The top of the dashboard answers the most immediate operational question: how many devices are working right now? The four state tiles (Online, Offline, GPS not updated, Other) use the connection-state definitions above.

| Panel                                    | What it shows                                                                                                                                                                                           |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Total objects** _(KPI)_                 | Every device registered in your account.                                                                                                                                                                |
| **Connection** _(donut chart)_            | The same four states shown as proportions, making it easier to see whether a significant share of the fleet is offline.                                                                                 |
| **Top 10 longest unreported** _(table)_   | The 10 objects with the oldest Last Updated timestamps, sorted from the longest gap to the shortest. Surfaces the devices most likely to need attention before drivers or customers report a problem. |

<details>

<summary>When to investigate using Top 10 longest unreported</summary>

Devices appearing here have gone the longest without contacting the platform. Common causes include dead or disconnected batteries, vehicles parked in areas with poor cellular signal (underground parking, remote sites), exhausted SIM data limits, and devices that have been physically moved or decommissioned without being removed from the account. Checking this table at the start of a shift is faster than sorting the Objects list by Last Updated.

</details>

## Movement

The movement section tells you what your fleet is actually doing, not just whether devices are reporting. Both panels draw from the same data, presented two ways, so they complement each other rather than repeat.

| Panel                                   | What it shows                                                                                                             |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Movement** _(donut chart)_            | The fleet split across the five movement states as proportions.                                                           |
| **Movement distribution** _(bar chart)_ | The same five movement states as absolute counts, making it easier to compare volumes when one state dominates the donut. |

<details>

<summary>Reading the movement distribution</summary>

The balance of states shifts predictably through the day. A fleet showing most objects as Parked early in the morning is normal before a shift starts. The same picture at midday suggests routes aren't running on schedule. A high Idling count at any time of day is worth noting: idling while stationary with the engine on accumulates fuel cost and engine wear that doesn't appear in trip distance or duration figures.

</details>

## Speed

**Average and max speed** plots fleet-wide average and peak speed over the last 24 hours. **Top 10 objects by speed** names the individual objects that produced those peaks.

<details>

<summary>When a speed spike means GPS error, not a speeding driver</summary>

An implausible reading in Top 10 objects by speed is more often a GPS error than an actual speed. Check the same object in Fleet details: a low satellite count or high HDOP at that time means the position, and the speed derived from it, is unreliable, not that the vehicle was actually going that fast.

</details>

## Geofences

**Geofences** shows where objects are right now relative to the zones configured in your account: every zone that currently contains at least one object, with the count and labels of those objects, sorted by object count from highest to lowest.

{% hint style="info" %}
This panel lists only zones that currently contain objects. Empty zones don't appear. For the complete list of configured zones, use [Map tools → Geofences](../tracking/map-tools/geofences.md).
{% endhint %}

## Points of interest

**Points of Interest** is the companion to Geofences for accounts organized around specific stops rather than larger zones: a customer site, a pickup point, a fuel station, a checkpoint. It lists every POI that currently has at least one object inside its radius, with the object count and an alphabetical list of object labels (objects without a label appear as `No Data`), sorted by object count from highest to lowest.

{% hint style="info" %}
The panel shows up to 100 POIs at a time, ranked by object count. POIs with no objects nearby don't appear. The panel updates within a few minutes of an object entering or leaving a POI radius.
{% endhint %}

## Fleet details

The **Fleet details** table is the detail layer beneath all the summary panels above. It lists every object in your account with its current state across all the dimensions the dashboard covers, plus position and address information.

Every column is sortable by clicking its header. HDOP (Horizontal Dilution of Precision) measures GPS fix quality; see "How to use Fleet details during an investigation" below for how to read it alongside satellite count.

{% hint style="info" %}
The table displays a maximum of 500 rows. If your account contains more than 500 objects, only the first 500 appear based on the current sort order.
{% endhint %}

<details>

<summary>How to use Fleet details during an investigation</summary>

Sorting by **Last Updated** brings the most recently active objects to the top, useful when verifying that a specific vehicle has checked in. Sorting by **Speed** surfaces the fastest objects at that moment. Sorting by **Address** groups objects by their current location, helping confirm that vehicles have reached expected sites. For objects flagged in the Top 10 longest unreported table, searching by object label in Fleet details shows their full current state alongside their last known coordinates.

A low satellite count or a high HDOP value alongside an unexpected location often indicates poor GPS signal rather than an actual position: the device is reporting, but the fix is unreliable. In open-sky conditions, HDOP values below 2 are typical. Values above 5 suggest treating the position with caution.

</details>

## Exporting panel data

Every panel here, both charts and tables, has the same CSV and PDF export options described in [Exporting data](README.md#exporting-data).
