# Technical conditions dashboard

The **Technical Conditions Dashboard** is the third tab of the Dashboards app, and it answers "how well is the fleet's hardware performing, and what needs attention?" (the [Fleet Live Status Dashboard](fleet-live-status-dashboard.md) covers "what is the fleet doing right now?" and the [Trips Dashboard](trips-dashboard.md) covers "what did the fleet do over the past week?"). It pulls GPS quality, power supply, mileage, fault codes, engine RPM, and safety events into a single view. That way, you can spot deteriorating devices and vehicles before they cause downtime.

{% hint style="info" %}
The Technical Conditions Dashboard is currently in beta (v1.0.0), an early version released to be shaped by real feedback. If something is missing, confusing, or could be more useful, use the **Send Feedback** button at the top of the tab. The four dashboards version independently, so the Fleet Live Status, Trips, Technical Conditions, and IoT Logic tabs may show different version numbers.
{% endhint %}

Want more customization and detail? [Dashboard Studio](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio) lets you build fleet analytics tailored to your own KPIs using IoT Query data. Recommended template to start with: [Fleet Anomaly Monitor](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/01-fleet-anomaly-monitor.md).

## How the dashboard defines fleet health

Several panels share the same status indicators, so it's worth reading the rules once before scanning the page.

An object counts as **active** if it has at least one hourly statistics record on file, meaning the device has communicated with the Navixy platform at some point in the stored history. Active doesn't mean active right now. An object that last reported a week ago still counts. An **inactive** object exists in your account catalog but has never produced an hourly record at all.

GPS quality combines two values reported by the device: the number of visible satellites and HDOP (Horizontal Dilution of Precision, a dimensionless measure of GPS geometry where lower is better).

| Status       | Condition                                 |
| ------------ | ----------------------------------------- |
| ⚫ No GPS Fix | HDOP is negative (unavailable or invalid) |
| 🔴 Critical  | HDOP > 5, or satellite count < 3          |
| 🟡 Warning   | HDOP > 2, or satellite count < 6          |
| 🟢 Normal    | HDOP ≤ 2 and satellite count ≥ 6          |

Battery and onboard (board) voltages are judged differently: each object's most recent reading is compared against that object's own historical average, rather than against a fixed threshold. This relative comparison catches gradual deterioration that absolute limits would miss, and accommodates fleets with mixed 12 V and 24 V vehicles.

| Status      | Condition                                                     |
| ----------- | ------------------------------------------------------------- |
| 🔴 Critical | Current voltage is more than 20% below the historical average |
| 🟡 Warning  | Current voltage is 10–20% below the historical average        |
| 🟢 Normal   | Current voltage is within 10% of the historical average       |

## Data window and refresh

The dashboard reads from the full hourly statistics history available for your account. There is no date picker, and the window can't be changed. In practice this covers roughly the last 7 days of hourly aggregates, which is the typical retention horizon for the underlying store.

The "latest" values shown in the health monitor tables and in **Latest diagnostics** always come from each object's most recent hourly record, regardless of how recent that record is. An object that last reported three days ago will still appear with its three-day-old values rather than being excluded.

The dashboard refreshes automatically every **90 seconds** while the browser tab is in focus, and the **Update** button in the top-right corner forces an immediate refresh.

## Fleet summary

The top row of three KPI tiles frames the overall size and activity of your fleet: **Total objects**, **Active objects**, and **Inactive objects**. Total objects is every device registered in your account, whether or not it has ever reported data. Active objects follows the active definition above, and Inactive objects is the difference between the two.

<details>

<summary>When to investigate a non-zero inactive count</summary>

A non-zero Inactive count is worth a look at your object catalog: newly installed devices that haven't started reporting, decommissioned hardware that's still listed, or GPS devices with configuration problems. The gap between Total and Active also affects how other panels read: a large inactive share doesn't mean your active fleet is performing badly.

</details>

## Safety and diagnostics event counts

The second row holds four KPI tiles: **GPS degraded events**, **SOS events**, **Seatbelt events**, and **DTC events count**. Each counts how many hourly periods recorded that type of event across the fleet, counting hours rather than individual events: an hour with five SOS activations and an hour with one both add one to the count. The exception is DTC events count, which sums raw event counts and so reflects cumulative OBD activity rather than the number of affected hours.

GPS degraded events counts hours with fewer than 3 visible satellites. Seatbelt events counts hours where the sensor reported the belt unfastened while the object was moving, even at very low speed.

These four tiles are fleet-wide counts only. To find which object triggered one, sort **Latest diagnostics** by the matching column.

## GPS quality

Two panels work together to surface devices with positioning problems. The bar chart tells you who has the most degraded hours. The health monitor tells you whose situation is getting worse.

| Panel                                                 | What it shows                                                                                                                                                                                                |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Top 10 objects by GPS degraded events** (bar chart) | The 10 objects with the most hours of poor GPS quality. An hour counts as degraded when satellites < 3, HDOP > 5, or HDOP is unavailable.                                                                    |
| **GPS health monitor** (table)                        | One row per object. Current HDOP and satellite count alongside each object's historical averages, with trend arrows (▲ rising, ▼ falling, ► flat) and a Require Attention status using the thresholds above. |

The GPS health monitor sorts No Fix first, then Critical, Warning, and Normal. Within each group, the worst readings sit at the top.

<details>

<summary>When to investigate GPS quality issues</summary>

Objects at the top of the bar chart often have antenna damage, obstructions on the device, or mounting problems. The health monitor adds context: an object with a Critical status but a stable trend has a known long-running issue, while a Warning status with a falling trend is a situation that's actively deteriorating and worth catching early.

</details>

## Battery and power supply

This section covers two distinct power supplies, each with its own trend chart and health monitor. **Battery voltage** is the device's internal backup battery, used when the vehicle is off or disconnected. **Board voltage** is the vehicle's main electrical system, typically 12 V for cars and light vehicles or 24 V for trucks and heavy equipment.

| Panel                                                     | What it shows                                                                                                                                                               |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Battery voltage trend for top 10 objects** (line chart) | Backup battery voltage over time for the 10 objects with the lowest current battery charge percentage.                                                                      |
| **Battery power supply health monitor** (table)           | Current voltage vs. historical average per object, with trend arrow, absolute difference, and relative change as a percentage. Sorted with the largest relative drop first. |
| **Board voltage trend for top 10 objects** (line chart)   | Onboard supply voltage over time for the 10 objects with the lowest current board voltage.                                                                                  |
| **Board power supply health monitor** (table)             | Same structure as the battery monitor, applied to the vehicle's onboard supply.                                                                                             |

Both health monitors use the Critical / Warning / Normal voltage thresholds described earlier.

<details>

<summary>When to investigate voltage trends</summary>

A downward voltage trend combined with a low charge percentage is the strongest signal that a device's backup battery is approaching failure.

Board voltage that drops while the engine is off is usually normal. A sustained drop while the engine is running indicates a charging or wiring problem. Comparing the board and battery monitors side by side is the quickest way to tell a device-side issue (battery degrading, board stable) from a vehicle-side issue (board degrading, battery may follow).

</details>

{% hint style="info" %}
Both health monitors only include voltage readings between 1 V and 50 V to filter out invalid sensor values. Objects whose readings all fall outside this range, typically because the sensor isn't fitted or isn't reporting, are excluded from the table entirely.
{% endhint %}

## Mileage

Two panels track odometer activity across the fleet, both using the hardware odometer reported by the device.

| Panel                                             | What it shows                                                                                                          |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Top 10 objects by mileage (km)** (table)        | The 10 objects with the highest current odometer readings, plus the timestamp of the message that produced each value. |
| **Mileage trend for top 10 objects** (line chart) | Odometer readings over time for those same 10 objects.                                                                 |

Unlike the rest of this dashboard, mileage isn't a fault signal. It's a service-planning view. **Top 10 objects by mileage (km)** names the vehicles that have accumulated the most distance, the usual starting point for distance-based service planning, not the ones with a technical problem.

Objects without a hardware odometer sensor don't appear in either panel, even if they have other telemetry.

<details>

<summary>When to investigate mileage patterns</summary>

A line that stays flat indicates a vehicle that has been parked or whose sensor has stopped reporting. A sharp vertical jump usually means a manual odometer correction or a sensor replacement, both of which are worth verifying against your maintenance records.

</details>

## Fault codes

The **Objects with active fault codes** table lists every hourly record in which one or more active diagnostic trouble codes (DTCs) were reported by the vehicle's OBD interface. Each row is one hourly record, not one object. The same vehicle may appear multiple times if it reported fault codes in different hours.

Codes appear as raw strings (for example, `P0300`). The dashboard doesn't decode them, so look up an unfamiliar code against your vehicle's OBD reference. When the table is empty, it shows "No DTC events found for the last 7 days." That message can mean either that no faults occurred or that none of your devices report DTCs over OBD at all. Check whether any object has ever populated the DTC columns in Latest diagnostics before concluding the fleet is clean.

<details>

<summary>When to investigate fault codes</summary>

A vehicle that appears many times with the same codes has a persistent fault that hasn't been cleared. A vehicle that appears once with many codes may have had a communication burst or a severe single event. Either case is worth a workshop visit.

</details>

## Engine and speed

Two panels cover engine behavior and peak speeds.

| Panel                                           | What it shows                                                                                                                                                                                                       |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Top 10 events by RPM** (table)                | The 10 individual hourly periods with the highest engine RPM readings recorded anywhere in the fleet, sourced from the vehicle CAN bus. Each row is one hour, and the value shown is the peak RPM during that hour. |
| **Speed trend for top 10 objects** (line chart) | Peak hourly speed over time for the 10 objects with the lowest current maximum speed, designed to surface vehicles whose top speed has been consistently low.                                                      |

When the RPM table is empty, it shows "No RPM values found for the last 7 days." As with DTC events, an empty result can mean no elevated readings occurred, or that none of your devices report engine RPM over the CAN bus at all.

<details>

<summary>When to investigate RPM and speed</summary>

RPM values above 10,000 are unusual for road vehicles and typically indicate either a genuine over-rev event or a sensor fault.

Consistently low top speeds in the speed trend chart can point to route-constrained assets, speed-limited equipment, or vehicles that have been parked for most of the data window.

</details>

## Latest diagnostics

The **Latest diagnostics** table at the bottom of the dashboard is the single-row-per-object summary of fleet health. It shows the most recent value of every metric the dashboard tracks for every object in your account. Those metrics include last update timestamp, battery level and voltage, board voltage, HDOP, satellite count, mileage, active DTC codes and event count, peak RPM and speed, SOS and seatbelt events, and peak acceleration. Every column is sortable.

<details>

<summary>How to use this table during an investigation</summary>

Sort by **Last Update** to find objects that have stopped reporting, the first thing to check when a vehicle appears stale in other panels. Sort by **Battery Voltage** or **Board Voltage** to find the lowest current readings without waiting for the health monitors to flag them. Sort by **DTC Events** or **SOS Events** to bring objects with current incidents to the top.

</details>

{% hint style="info" %}
The table shows up to 600 objects, ordered by most recently updated first. If your account has more than 600 objects, the least recently updated ones won't appear here. Use the other panels to investigate them.
{% endhint %}

## Exporting panel data

Every panel here, both charts and tables, has the same CSV and PDF export options described in [Exporting data](README.md#exporting-data).
