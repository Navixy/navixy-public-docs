---
description: >-
  Trips & Stops in Reports: how Navixy detects trips and stops, how it
  calculates distance, duration, and speed, and what each report column shows.
tags:
  - reference
  - beta
---

# Trips & Stops

**Trips & Stops** shows how your assets moved over a period. For each trip, the report shows when and where the trip started and ended. It also shows how far and how fast the asset went, and how long the asset stood still between trips. Use it to check mileage, to see where a vehicle spent its day, or to compare the activity of vehicles.

This page describes what the report shows and how its values are calculated. To generate the report, see [Manage reports](manage-reports.md).

{% hint style="info" %}
**Access**

**Trips & Stops** is in your Reports menu when your role gives access to it. See [Access to Reports](README.md#access-to-reports).
{% endhint %}

## What the report shows

<figure><img src="../.gitbook/assets/reports-trips-and-stops.png" alt="The Trips & Stops report for cars and trucks over four days. The tiles show 124 trips, 92 stops, a total trip duration of 131 hours 35 minutes, a total stop duration of 110 hours 16 minutes, and a total distance of 4,504.8 km. The table lists the trips grouped by day, with a row number and the columns Asset, Start, End, Trips Duration, Trips Distance, Stops duration, Avg speed, and Max speed. The Start address and End address columns are hidden."><figcaption>The Trips & Stops report for cars and trucks, with the address columns hidden</figcaption></figure>

Here's what you can do in the report:

* Choose the asset types, the assets, and the period, and click **Generate**.
* Read the totals for the period in the **Trips**, **Stops**, **Trips Duration**, **Stops duration**, and **Trips Distance** tiles.
* Show trips, stops, or both in the table with the **Trips** and **Stops** buttons. You can also click the **Trips** tile or the **Stops** tile.
* Open a tab for one asset, or a tab that groups the rows by day or by asset type.
* Click a trip to open its details: a map, the trip values, and the geo objects that the trip passed through.
* Choose the columns in **Customize**.

The report includes every selected asset that has a GPS device. An asset without a GPS device doesn't appear in the report, even when you select it. When an asset has more than one GPS device, trips, stops, and geo object visits use only the data of its primary device. The primary device is the device that gives the asset its position. To learn how to choose it, see [Manage assets](../bdr/assets/manage-assets.md).

## How a trip is detected

A trip starts when the asset starts to move and ends when it stands still for at least 5 minutes. Navixy detects the start and end of a trip from the speed that the GPS device reports, not from the ignition state.

The rules are the same for every workspace:

* The asset is moving when its speed is above 3 km/h.
* A standstill shorter than 5 minutes, such as at a traffic light or in a traffic jam, is part of the trip. The time of the standstill counts as parked time of the trip.
* When the GPS device sends no data for more than 15 minutes, the trip ends at the last position before the gap. The trip appears in reports when the GPS device sends data again, or about 24 hours after the trip started.
* A trip must cover at least 50 m. A shorter movement, such as moving a vehicle inside a yard, isn't a trip.
* A trip that lasts longer than about 24 hours is split into more than one trip.

Before Navixy detects trips, it leaves out positions that aren't reliable:

* Positions from a mobile network or Wi-Fi
* Positions with fewer than 4 satellites or with low accuracy
* Positions with a speed above 200 km/h
* Sudden jumps in position

Because of this filter, a sudden jump doesn't add distance to a trip. In a place with poor reception, such as an underground garage, Navixy uses fewer positions for the trip. The trip can then be shorter than the real one, or missing.

## How a stop is detected

A stop is a period when the asset stands still for at least 5 minutes and the GPS device keeps sending positions. Navixy detects stops from speed, the same way as trips: the asset stands still when its speed is 3 km/h or less.

A stop needs data. Many GPS devices send nothing or very little while the ignition is off. For such a device, the report can show no stop between the two trips. It can also show a stop that ends when the device stops sending data.

## How the trip values are calculated

Each trip has these values:

| Value | How it is calculated |
| --- | --- |
| Duration | The time from the start of the trip to its end. |
| Moving time | The part of the trip when the speed was above 3 km/h. |
| Parked time | The part of the trip when the speed was 3 km/h or less, including standstills shorter than 5 minutes. |
| Distance | The difference in the odometer reading between the start and the end of the trip, when the GPS device sends odometer readings. Otherwise, the sum of the distances between positions. |
| Average speed | The distance divided by the moving time. |
| Maximum speed | The highest speed in the trip, not counting speeds above 200 km/h. |

Two of these values are often misread:

* Average speed uses moving time, not the duration. A trip with long traffic jams has a higher average speed than its distance divided by its duration.
* Parked time isn't the same as **Parked** in Dashboards. In the Live status dashboard, **Parked** means standing still for 5 minutes or more, which Reports calls a stop. Pauses shorter than 5 minutes are **Stopped** there.
* Parked time isn't engine idling. It's the time inside the trip at 3 km/h or less, whether the engine was running or not. Reports doesn't calculate engine idling.

Because the odometer is used when it's available, the trip distance can differ from the length of the track on a map.

## Table columns

The table shows one row for each trip. With **Stops** on, it also shows one row for each stop. Rows are grouped by day. The heading of each day shows the number of trips, their total duration, and their total distance. The **Total** row at the top shows the totals for the whole table, and the **#** column numbers the rows.

These columns are shown by default:

| Column | What it shows |
| --- | --- |
| **Asset** | The name of the asset in Business Data Repository. Click the name to open a tab for the asset. |
| **Start**, **End** | The time of the first position above 3 km/h, and the time of the last position above 3 km/h before the asset stopped. So **End** is when the asset stopped, not 5 minutes later when Navixy confirmed the stop. The date is in the day heading above the rows. |
| **Start address**, **End address** | The name of the geo object where the trip started or ended. The cell is empty when the trip started or ended outside every geo object. |
| **Trips Duration** | The duration of the trip, in hours and minutes. |
| **Trips Distance** | The distance of the trip, in kilometers. |
| **Stops duration** | How long the asset stood still after this trip. |
| **Avg speed**, **Max speed** | The average and maximum speed of the trip, in km/h. |

You can also add **Start coordinates** and **End coordinates**: the latitude and longitude of the start and end of the trip. To change the columns and their order, open **Customize**.

A stop row shows the asset and the time when the stop started and ended. **Start address** shows the name of the geo object where the asset stood. When you add **Start coordinates**, the column shows the coordinates of the stop. **Stops duration** shows the duration of the stop. The other cells of a stop row have no value.

The address columns show the names of geo objects only, not street addresses. Only a geo object drawn as an area or a circle counts. A geo object drawn as a point or a line doesn't fill these columns. A new geo object appears in reports within about 30 minutes. To add a name for a site, see [Create geo object](../bdr/geo-objects/create-geo-object.md).

## Report tabs

The **Summary** tab shows all selected assets, grouped by day. To add a tab, click **+** and choose one of the following:

* **Days**: The same grouping by day as **Summary**, in a separate tab.
* **Types**: The rows grouped by asset type.
* An asset from the list: A tab for one asset, with its trips and stops grouped by day.

On the tab of one asset, the table can also show events as rows between the trips and stops. Use **Event types** to choose which events are shown. The list has **Trip start / Stop end**, **Trip stop / Stop start**, **Ignition on**, **Ignition off**, **Geofence entrance**, and **Geofence exit**. All of them are selected by default. An event row shows the time of the event and where it happened.

## Trip details

Click a trip to open its details. The details show the following sections:

* **Map**: The GPS track of the trip, from its start to its end, and the geo objects that the asset visited. Point to the state timeline to see where the asset was at that time.
* **Trip metrics**: The duration, the distance, the average and maximum speed, and the parked time of the trip.
* **State timeline**: When the asset was moving or stopped (**Movement**), when the ignition was on or off (**Ignition**), and when the asset was inside a geo object (**Geofences**).
* **Geofence visits**: Each geo object that the asset entered during the trip, with the times when it entered and left. A geo object that the asset was already inside when the trip started isn't listed.
* **Event timeline**: The events of the trip, such as geo object entries and exits, in time order. To save them as a file, click **Export CSV**.

To go to the previous or the next trip, use the arrows at the top of the details. To print the details, click **Export PDF**.

Navixy decides whether the asset is inside a geo object position by position. There is no minimum time inside and no margin at the border. When an asset moves along the border of a geo object, small GPS drift can put one position outside and the next one inside. This adds extra **Geofence entrance** and **Geofence exit** rows, and short visits in **Geofence visits**.

## See also

* [How report data is prepared](how-report-data-works.md): Learn when a trip appears in a report and which time zone applies.
* [Manage reports](manage-reports.md): Generate the report, save it, and export it.
* [Troubleshooting reports](troubleshooting.md): Find out why a trip is missing or an address column is empty.
