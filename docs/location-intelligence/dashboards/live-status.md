---
description: >-
  The panels of the Live status dashboard: connection and movement status of
  your assets, speed over 24 hours, geofences, and the latest data per asset.
tags:
  - reference
  - beta
---

# Live status

**Live status** shows the current state of every asset in your workspace. Use it during a shift to see which assets report, which of them move, and which stopped reporting. It's the first dashboard in [Dashboards](./).

<figure><img src="../.gitbook/assets/dashboards-live-status.webp" alt="The Live status dashboard with 14 assets. The numbers at the top show 14 assets in total, 14 online, and 0 offline, GPS not updated, and other. Below them, a donut chart shows that all 14 assets are online, and a table lists the assets that haven't reported the longest, with the time of their last message."><figcaption>Live status for a fleet of 14 assets, all of them online</figcaption></figure>

Here's what you can do:

* Check how many assets report right now, in [Connection status](#connection-status).
* See which assets move, stand, or idle, in [Movement status](#movement-status).
* Find the assets that stopped reporting, in [Assets that stopped reporting](#assets-that-stopped-reporting).
* Compare the speed of the fleet over the last 24 hours, in [Speed over the last 24 hours](#speed-over-the-last-24-hours).
* See which assets are inside each geofence, in [Geofences](#geofences).
* Look up the latest data of one asset, in [Fleet details](#fleet-details).

## Connection status

The connection status shows whether the GPS device of an asset is sending messages. When an asset has several devices, its status comes from the [primary device](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset), the device whose position becomes the position of the asset. Navixy sets the status from the time that has passed since Navixy last received a message:

| Status on the dashboard | Meaning |
| --- | --- |
| **Online** | Navixy received a message in the last 5 minutes. |
| **GPS not updated** | No message for more than 5 minutes and up to 1 hour. |
| **Offline** | No message for more than 1 hour. |
| **Other** | The device hasn't sent a message yet, or Navixy has no status for the asset. |

The time counts from the moment when Navixy received the last message, not from the time that the device wrote into it. The next message makes the asset **Online** again. Despite its name, **GPS not updated** doesn't mean a message without a position: it means that no message arrived at all.

The map in [Tracking](../tracking/) uses its own rule and shows a device as offline after 10 minutes without a message. So between 10 minutes and 1 hour, an asset is offline on the map and **GPS not updated** on this dashboard.

These panels show the connection status:

* **Total assets:** All assets in your workspace.
* **Online**, **Offline**, **GPS not updated**, and **Other:** The number of assets with each status.
* **Connection:** The same four groups as a donut chart, with their shares of the fleet.

The four status numbers count only assets that have a device assigned. So an asset without a device counts in **Total assets** but in none of the four statuses. Each asset with a device has exactly one status, so the four numbers add up to the assets that have a device.

## Movement status

The movement status shows whether an asset is driving or standing, from the speed in the latest position of its primary device:

| Status on the dashboard | Meaning |
| --- | --- |
| **Moving** | The speed is 3 km/h or more. |
| **Idle** | The ignition is on, but the asset is stopped or parked. |
| **Stopped** | The speed has been below 3 km/h for less than 5 minutes, for example at a traffic light. |
| **Parked** | The speed has been below 3 km/h for 5 minutes or more. |
| **Unknown** | The latest position has no speed or no GPS fix, or Navixy has no status for the asset. |

A message without a position, such as a sensor reading, doesn't change the movement status. The movement status also doesn't change while no message arrives: an asset that stopped and then went silent stays **Stopped** and doesn't become **Parked**.

**Parked** on this dashboard is what [Trips, 14 days](trips.md) calls a stop between two trips. **Stopped** is a short halt inside a trip. **Idle** needs the ignition to be on, unlike the idle time on Trips, 14 days, which counts stops with the ignition on or off.

**Movement distribution** shows the number of assets with each status as a bar chart. **Movement** shows the same numbers as a donut chart. If you need only one of them, hide the other, as described in [Search, layout, and export](dashboard-tools.md).

## Assets that stopped reporting

**Top 10 longest unreported** lists the 10 assets whose last status update is the oldest, with its time in **Last updated**. It helps you find a device that lost power or connection.

For a silent asset, the last status update is the moment when Navixy marked it **Offline**, about 1 hour after its last message. An asset whose device has never sent a message comes first, with an empty time. When the last status update is more than 30 days old, the table shows "1 month+" instead of the time.

The usual reasons why a device goes silent are on the device side:

* The battery of the device is dead or disconnected.
* The vehicle is in a place with poor mobile coverage, such as an underground car park.
* The data allowance of the SIM card is used up.
* The device was removed or retired, but it's still assigned to the asset in Navixy. Check the devices of the asset.

## Speed over the last 24 hours

Two panels show speed over the last 24 hours:

* **Average and max speed:** A line chart with one point per hour. One line is the average speed of the fleet in that hour. The other line is the highest speed that any asset reached in that hour.
* **Top 10 assets by speed:** The 10 assets with the highest speed in the last 24 hours.

**Top 10 assets by speed** shows the speed as the GPS device sent it, without removing implausible values. An implausibly high speed can come from a GPS error. To see which assets have many positions with poor accuracy, see [GPS accuracy](conditions.md#gps-accuracy) on the Conditions dashboard.

The average counts every status update of the hour, including the updates of assets that don't move. So a low average can mean that most of the fleet was parked, not that the fleet drove slowly.

## Geofences

**Geofences** lists every geofence that has assets inside it. Navixy updates this list every 30 minutes. For each geofence, it shows the number of assets and their names. The geofence that has the most assets comes first. A geofence here is a geo object from [Business Data Repository](../bdr/geo-objects/). A geo object without a name appears as its number, such as `#104`. The panel shows up to 100 geofences.

## Fleet details

**Fleet details** shows the latest data of every asset, one row per asset, with the most recent status update first. It shows up to 500 assets.

| Column | What it shows |
| --- | --- |
| **Asset label** | The name of the asset. An asset without a name appears as its ID. |
| **Last updated** | The time of the latest status update: the latest message, or the moment when Navixy changed the connection status because no message arrived. |
| **Offline category** | How long ago **Last updated** was: "24+hrs", "3+days", "7+days", "14+days", or "30+days". Empty when **Last updated** is less than 24 hours ago. |
| **Last connection status**, **Last movement status** | The latest connection and movement status of the asset. The values are listed under the table. |
| **Speed**, **Altitude**, **Heading** | The speed, the height above sea level, and the direction of travel from the latest position. The heading appears as a compass arrow and direction, such as "↗ NE". |
| **HDOP** | GPS accuracy of the latest position. HDOP (horizontal dilution of precision) is a number that the GPS device reports: the lower it is, the more accurate the position. |
| **Coordinates**, **Address** | The latest position, and the street address at that position. |
| **Current geofence** | The geofences that the asset is inside, updated every 30 minutes. |

**Last connection status** shows one of these values:

* `online`: A message arrived in the last 5 minutes.
* `standby`: No message for more than 5 minutes and up to 1 hour.
* `offline`: No message for more than 1 hour.

**Last movement status** shows one of these values:

* `moving`: The speed is 3 km/h or more.
* `stopped`: The speed has been below 3 km/h for less than 5 minutes.
* `parked`: The speed has been below 3 km/h for 5 minutes or more.
* `unknown`: The latest position has no speed or no GPS fix.

**Last movement status** has no idle value. An asset that idles appears as `stopped` or `parked`. A cell shows "—" when Navixy has no status for the asset.

**Offline category** counts from **Last updated**, which for a silent asset is about 1 hour after its last message. So "24+hrs" appears about 25 hours after the last message, much later than the **Offline** status. An asset that has been silent for a few hours is **Offline**, but its **Offline category** is still empty.

The table also has a **Satellites** column. To choose which columns you see, use **Columns** in the panel header.

## See also

* [Trips, 14 days](trips.md): Learn how the fleet was used over the last 14 days.
* [Conditions, 14 days](conditions.md): Learn which devices report poor GPS accuracy or falling voltage.
* [Search, layout, and export](dashboard-tools.md): Learn how to hide panels, choose columns, and export the data of a panel.
