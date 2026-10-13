---
description: >-
  Run trip, stop, and sensor reports for the assets and the period that you
  choose, and download the tables as CSV or Excel files.
tags:
  - overview
  - beta
noIndex: true
---

# Reports

Reports answers the questions that a fleet manager asks about a whole period. How far did each van drive last week, how long did it stand still, and how warm did the refrigerated trailer get? Reports shows how your fleet worked over the period that you choose. You see the totals first, then one asset, trip, or sensor. [Tracking](../tracking/) shows where your assets are now, and Reports shows what they did.

<figure><img src="../.gitbook/assets/reports-trips-and-stops.png" alt="The Trips and stops report on the Summary tab, next to the Reports menu. The menu lists Ask and the report types in four groups: Fleet activities, Sensors and equipment, Safety and security, and Employee activities. Trips and stops and Sensor readings have no lock, and the other report types and Ask show a lock. At the top of the report are the All assets button, the period button for yesterday, and the Run button, with the time zone of the report under them. Six tiles show the totals: Trips, Distance, Driving time, Stops, Stop time, and Max speed. The By asset table has one row for the asset of the report. Under it, the Trip and stop log lists trips and stops with their start, end, duration, distance, driving time, speeds, and coordinates."><figcaption><p>Trips and stops: the totals for the period, one row for each asset, and the log of trips and stops</p></figcaption></figure>

## What you can do with Reports

**See how your fleet moved, from the totals to a single trip.** At the end of a week or a month, the [Trips and stops](trips-and-stops.md) report shows the totals for the period in tiles. The tiles show the number of trips and stops, the distance, the driving time, the stop time, and the maximum speed. The **By asset** table splits these totals by asset, and the **By day** table splits them by day. Open a tab for one asset to see its days. The **Trip and stop log** lists every trip and stop with its start, end, duration, distance, and speed. Click a trip in the log to see where it started and ended on a map.

**Check what the sensors reported.** When a load arrives too warm or a tank runs low, the [Sensor readings](sensor-readings.md) report shows the readings of every sensor on the GPS devices of your assets over a period. Sensors measure values such as fuel level, temperature, or battery voltage. For each sensor, the report shows the average, the lowest and the highest reading, how much the value changed, and how much of the period has readings. A chart for each unit of measure shows how the readings changed. Click a sensor to open its reading panel, which also lists the pauses: the gaps when the sensor sent no readings for longer than usual.

**Share a report and download its tables.** The page address keeps the assets and the period that you chose, so a link to the page opens the same report. Hide the columns that you don't need and download each table as a CSV or Excel file. See [Manage reports](manage-reports.md) to learn how to run a report, share it, and download its tables.

{% hint style="info" %}
**Navigation**

Reports is a Location Intelligence product. To open it, click **Reports** on the sidebar. Reports opens the first report type that you have access to in the Reports menu.
{% endhint %}

## Who it is for

Reports is for the people who manage a fleet and are responsible for it:

* A fleet manager or an operations manager reviews how far the vehicles drove and how long they stood still.
* A dispatcher checks the trips and stops of the day before.
* A maintenance manager checks what the sensors of vehicles and equipment reported.
* An analyst finds the assets and trips behind a total.

A report gives each of them one record of what happened over a period, in one table. They don't have to check each vehicle on the map.

Reports has a set of ready report types. To build your own reports and dashboards from the same kind of data, use [IoT Query Premium](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/75iUkTidqKcVcd2J49aB/). Its AI assistant builds a dashboard from a request in plain language.

## Core concepts

* **Report type**: What a report answers. The Reports menu groups the report types under **Fleet activities**, **Sensors and equipment**, **Safety and security**, and **Employee activities**. You can run two report types: [Trips and stops](trips-and-stops.md) and [Sensor readings](sensor-readings.md). The other report types and **Ask**, which builds a report from a request in plain language, show a lock and are offered as paid options. See [Report types with a lock](manage-reports.md#report-types-with-a-lock).
* **Report**: A report type run for the assets and the period that you choose.
* **Trip**: A movement of an asset from the moment it starts to move until it stands still for at least 5 minutes.
* **Stop**: A period of at least 5 minutes when the asset stands still and its GPS device keeps sending positions.

## Where report data comes from

A report is about your assets, not about GPS devices. Reports combines two things:

* **What the GPS devices send**: Positions, speed, and sensor values.
* **Your assets in** [**Business Data Repository**](../bdr/): A report covers the vehicles, people, or containers that the GPS devices are assigned to.

A report shows data up to the moment when you run it. See [How report data is prepared](how-report-data-works.md) to learn when data appears, how far back it goes, and which time zone a report uses.

## Before you start

Check the following before you run your first report:

* **You have access to Reports.** Reports is on the sidebar when your workspace includes Reports and one of your roles includes it. If it isn't on the sidebar, ask the people who set up your workspace. See [Access to Reports](./#access-to-reports).
* **Your assets have GPS devices.** An asset without an assigned GPS device isn't included in any report. Assign the device before you need the data. Trips that a device recorded before it was assigned to the asset never appear in the reports of this asset. See [Manage assets](../bdr/assets/manage-assets.md).

## Access to Reports

Your role decides which report types you can use. Reports has no access settings of its own.

A role gives access to each report type separately. It can give you one or both of these permissions:

| Access | What it allows |
| --- | --- |
| See the report type | The report type is in the Reports menu, and you can open it |
| Run the report | You can run a report of this type |

To run a report, you need both permissions. A report type that you can't see isn't in the Reports menu, so you can't run it either. If you can see a report type but not run it, its page shows this message instead of the report: "You can see this report but not run it. Running it takes a role with the run permission, which is granted in Console."

The [report types with a lock](manage-reports.md#report-types-with-a-lock) appear in the Reports menu for everyone, whatever their role.

A report can include every asset in the workspace, including an asset that your role doesn't let you see in **Assets**.

If you need a report type that isn't in your Reports menu, ask the people who set up your workspace. See [Access and roles](../access-and-roles.md) to learn how roles give access to the products.

## Where to start

Start with [Manage reports](manage-reports.md). It takes you from opening Reports to your first report for the assets and the period that you choose. To learn what each figure and column means, read [Trips and stops](trips-and-stops.md) and [Sensor readings](sensor-readings.md). If a report is empty or a trip is missing, see [Troubleshooting reports](troubleshooting.md).

## Section contents

* [Manage reports](manage-reports.md): Run a report, share it by its link, and download its tables.
* [How report data is prepared](how-report-data-works.md): Learn when new data appears in a report, how far back reports go, and which time zone a report uses.
* [Trips and stops](trips-and-stops.md): Learn how trips and stops are detected and what the tiles, tables, and log of the report show.
* [Sensor readings](sensor-readings.md): Learn which values the report includes and how the average, the change, and the coverage are calculated.
* [Troubleshooting reports](troubleshooting.md): Find out why a report is empty, a trip is missing, or you can't open a report.
