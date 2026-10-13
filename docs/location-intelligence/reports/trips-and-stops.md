---
description: >-
  Trips and stops in Reports: how Navixy detects trips and stops, how it
  calculates the trip values, and what the tabs, tables, log, and panels show.
tags:
  - reference
  - beta
---

# Trips and stops

**Trips and stops** shows how your assets moved over a period. For each trip, the report shows when the trip started and ended, the distance and the speed, and the start and end coordinates. For each stop, it shows when and for how long the asset stood still. Use it to check mileage, to see how long each vehicle drove in a day, or to compare the activity of vehicles.

This page describes what the report shows and how its values are calculated. To run the report, see [Manage reports](manage-reports.md#run-a-report).

{% hint style="info" %}
**Access**

**Trips and stops** is in your Reports menu when your role gives access to it. See [Access to Reports](README.md#access-to-reports).
{% endhint %}

## What the report shows

<figure><img src="../.gitbook/assets/reports-trips-and-stops.png" alt="The Trips and stops report on the Summary tab, next to the Reports menu. The menu lists Ask and the report types in four groups: Fleet activities, Sensors and equipment, Safety and security, and Employee activities. Trips and stops and Sensor readings have no lock, and the other report types and Ask show a lock. At the top of the report are the All assets button, the period button for yesterday, and the Run button, with the time zone of the report under them. Six tiles show the totals: Trips, Distance, Driving time, Stops, Stop time, and Max speed. The By asset table has one row for the asset of the report. Under it, the Trip and stop log lists trips and stops with their start, end, duration, distance, driving time, speeds, and coordinates."><figcaption><p>Trips and stops on the Summary tab: the totals in the tiles, a row for each asset, and the log of trips and stops</p></figcaption></figure>

Here's what you can do in the report:

* Choose the assets and the period at the top of the report, and click **Run**.
* Read the totals for the period in the six tiles: **Trips**, **Distance**, **Driving time**, **Stops**, **Stop time**, and **Max speed**.
* Compare assets in the **By asset** table, and compare days in the **By day** table.
* Open a tab for one asset to see only the trips and stops of that asset.
* Show all lines, only trips, or only stops in the **Trip and stop log**.
* Click a line in the log to open the trip or stop panel, with a map.
* Hide columns and download a table as a file. See [Show and hide columns](manage-reports.md#show-and-hide-columns) and [Download a table](manage-reports.md#download-a-table).

The report includes the trips and stops of every GPS device of an asset. When two GPS devices of one asset both record a trip, the report shows the trip twice. The tiles and the tables then count it twice. In the log, the **Device** column appears when an asset's lines come from more than one GPS device.

## How a trip is detected

A trip starts when the asset starts to move and ends when it stands still for at least 5 minutes. Navixy detects the start and end of a trip from the speed that the GPS device reports, not from the ignition state.

The rules are the same for every workspace:

* The asset is moving when its speed is above 3 km/h.
* A standstill shorter than 5 minutes, such as at a traffic light or in a traffic jam, is part of the trip. Its time counts in the duration of the trip, but not in the driving time.
* When the GPS device sends no data for more than 15 minutes, the trip ends at the last position before the gap. The trip appears in reports when the GPS device sends data again, or about 24 hours after the trip started.
* A trip must cover at least 50 m. A shorter movement, such as moving a vehicle inside a yard, isn't a trip.
* A trip that lasts longer than about 24 hours is split into more than one trip.

Before Navixy detects trips, it leaves out positions that aren't reliable:

* Positions from a mobile network or Wi-Fi
* Positions with fewer than 4 satellites or with low accuracy
* Positions with a speed above 200 km/h
* Sudden jumps in position

Because of this filter, a sudden jump doesn't add distance to a trip. In a place with poor reception, such as an underground garage, Navixy uses fewer positions for the trip. The trip can then be shorter than the real trip, or missing from the report.

## How a stop is detected

A stop is a period when the asset stands still for at least 5 minutes and the GPS device keeps sending positions. Navixy detects stops from speed, the same way as trips: the asset stands still when its speed is 3 km/h or less.

A stop needs data. Many GPS devices send nothing or very little while the ignition is off. For such a device, the report can show no stop between two trips. It can also show a stop that ends when the device stops sending data.

## How the trip values are calculated

Each trip has these values:

| Value | How it's calculated |
| --- | --- |
| **Duration** | The time from the start of the trip to its end. |
| **Driving time** | The part of the trip when the speed was above 3 km/h. |
| **Distance** | The difference in the odometer reading between the start and the end of the trip, when the GPS device sends odometer readings. Otherwise, the sum of the distances between positions. |
| **Avg speed** | The distance divided by the driving time. |
| **Max speed** | The highest speed in the trip, not counting speeds above 200 km/h. |

Two of these values are often misread:

* **Avg speed** uses the driving time, not the duration. A trip with long traffic jams has a higher average speed than its distance divided by its duration.
* **Driving time** leaves out the standstills shorter than 5 minutes inside the trip. It isn't engine time. When the asset stands still during a trip, that time counts in **Duration** but not in **Driving time**, whether the engine runs or not. Reports doesn't calculate engine idling.

The trip distance can differ from a distance calculated from the GPS positions, because Navixy uses the odometer when it's available.

## Tabs and tables

Each tab shows the six tiles, a table, and the **Trip and stop log**. The tabs differ in what they cover:

* **Summary**: All assets of the report. The table is **By asset**, with a row for each asset.
* **Days**: All assets of the report. The table is **By day**, with a row for each day of the period.
* A tab for an asset: Only that asset. The table is **By day**, with a row for each day of the period.

To open a tab for an asset, click the asset in the **By asset** table. You can also click <picture><source srcset="../.gitbook/assets/icons/reports-add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-add.svg" alt="Open a tab" data-size="line"></picture> next to the tabs and choose the asset. Up to 20 tabs for assets can be open. When you open another asset tab, the oldest asset tab closes.

The tiles and both tables show the same figures. The tiles show them for everything on the tab, and the tables show them for each asset or each day:

| Figure | What it shows |
| --- | --- |
| **Trips** | The number of trips. |
| **Distance** | The total distance of the trips. A trip without a distance value isn't counted. |
| **Driving time** | The total driving time of the trips. |
| **Stops** | The number of stops. |
| **Stop time** | The total duration of the stops. |
| **Max speed** | The highest maximum speed of the trips. |

The **By asset** table starts with the **Asset** column, and the **By day** table starts with the **Day** column. Every asset of the report has a row in **By asset**, including an asset with no trip or stop in the period. Every day of the period has a row in **By day**, including a day with no trip or stop. To sort a table, click a column heading.

Distances are in kilometers, speeds are in km/h, and durations are in hours and minutes. Times and days are in your browser's time zone. See [Which time zone a report uses](how-report-data-works.md#which-time-zone-a-report-uses).

As the note under the **By asset** table says, "A trip or a stop counts in full in the period it starts in." The note under the **By day** table says, "A trip or a stop counts in full on the day it starts on." A trip that started before the period isn't in the report, even when it ended inside the period. In the same way, a trip that crosses midnight counts in full on the day that it started.

## Trip and stop log

The **Trip and stop log** lists each trip and each stop as a line, in the order that they started. The tab decides how the lines are grouped:

* **Summary**: By asset, then by day.
* **Days**: By day, then by asset.
* A tab for an asset: By day.

When the report covers one asset, the log doesn't group by asset. When the period is one day, the log doesn't group by day. Each group heading shows the six tile figures for the lines in that group.

To choose which lines the log shows, click **All**, **Trips**, or **Stops** next to the log title. Each button shows the number of its lines. With **Trips** or **Stops**, the log leaves out the **Type** column and the columns for the other kind of line. The group headings then show only the figures for trips or for stops.

The log has these columns:

| Column | What it shows |
| --- | --- |
| **Type** | **Trip** or **Stop**. |
| **Start** | The time when the trip or stop started. The day is in the group heading, or is the day of a one-day period. |
| **End** | The time when the trip or stop ended. When it ended on a later day, the date and the time. For a trip, the end time is when the asset stopped, not 5 minutes later when Navixy confirmed the stop. |
| **Duration** | How long the trip or stop lasted. |
| **Distance**, **Driving time**, **Avg speed**, **Max speed** | The values of the trip. See [How the trip values are calculated](#how-the-trip-values-are-calculated). |
| **Position** | The latitude and longitude. For a stop, one point: where the asset stood. For a trip, the start point and the end point, with an arrow (→) between them. |
| **Device** | The GPS device that recorded the trip or stop. The column appears when an asset's lines come from more than one GPS device. |

A stop line leaves the **Distance**, **Driving time**, **Avg speed**, and **Max speed** cells empty.

## Trip or stop panel

Click a line in the log to open its panel at the side of the page. The panel has the title **Trip** or **Stop**, with the asset and the GPS device under it. The panel shows the following:

* The figures: **Start** and **End**, with the date and the time, and **Duration**. A trip panel also shows **Driving time**, **Distance**, **Avg speed**, and **Max speed**.
* A map. For a trip, it shows a start pin with a play sign and an end pin with a flag. For a stop, it shows one pin with a parking sign.
* The coordinates: **Start point** and **End point** for a trip, or **Position** for a stop.

## See also

* [How report data is prepared](how-report-data-works.md): Learn when a trip appears in a report, how far back report data goes, and which time zone a report uses.
* [Manage reports](manage-reports.md): Run the report, share it by its link, and download its tables.
* [Troubleshooting reports](troubleshooting.md): Find out why a trip is missing or why the report is empty.
