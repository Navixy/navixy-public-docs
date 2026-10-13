---
description: >-
  When trips and sensor readings appear in Reports, how long report data is
  kept, what happens to late data, and which GPS devices and time zone apply.
tags:
  - fundamentals
  - beta
---

# How report data is prepared

Reports doesn't read your GPS devices directly. Navixy prepares trips, stops, and sensor readings from device data and stores them. When you run a report, Navixy combines this stored data with trips, stops, and readings that it calculates from the newest device data. This page explains when data appears, how long it is kept, and which data a report includes. These rules apply to Trips and stops and to Sensor readings, except where a section names a single report type.

## When new data appears in a report

A report shows trips, stops, and sensor readings up to the moment when you run it. The report doesn't update by itself while it is open. To see newer data, [run the report again](manage-reports.md#run-a-report).

Navixy adds a trip to report data only after the trip ends. A trip ends when the asset stands still for at least 5 minutes. As a result, a trip that is in progress isn't in any report.

When the GPS device sends no data for more than 15 minutes, the trip ends at the last position before the gap. Navixy adds this trip when the GPS device sends data again, or about 24 hours after the trip started. To see where an asset is now, use [Tracking](../tracking/).

## How far back report data goes

Navixy keeps each kind of report data for a fixed time. Navixy keeps sensor readings in steps. A step groups one sensor's values over a fixed time, from 5 minutes to 1 hour. [Sensor readings](sensor-readings.md#how-readings-are-grouped-into-5-minute-steps) explains how steps work.

| Data | Kept for |
| --- | --- |
| Trips and stops | 6 months |
| Sensor readings, in 5-minute to 30-minute steps | 3 months |
| Sensor readings, in 1-hour steps | 12 months |

A report has no data for a period that is older than the time shown in the table. For sensor readings from 3 to 12 months ago, Navixy keeps only the 1-hour steps.

A report can cover a period of up to 31 days. The earliest report data is from the time when your workspace was created.

## Data that arrives late isn't added

Data that reaches Navixy long after the GPS device recorded it is left out of reports, because Navixy doesn't change report data that it already stored. Late data is common after a GPS device was offline: the device sends its stored data when it connects again.

A position that arrives after a newer position from the same GPS device is also left out of trips. As a result, for a period when a GPS device was offline, a report can miss a trip or show a shorter trip.

## A GPS device must be assigned to the asset first

A report includes only the trips and readings that Navixy prepared while the GPS device was assigned to an asset. Data from before the assignment isn't added, even after you assign the device.

When you move a GPS device to another asset, the earlier trips and readings stay with the old asset. New trips and readings go to the new asset. The change applies within about 30 minutes.

Both report types include every GPS device of the asset. Trips and stops includes the trips and stops of each device, and Sensor readings shows the readings of each device. When two GPS devices of one asset record the same trip, [Trips and stops](trips-and-stops.md) shows the trip twice, once for each device.

## Which time zone a report uses

A report uses your computer's time zone, as your browser reports it. Reports names this time zone under the assets and the period at the top of the report, in the line "Times are in …".

The period that you choose, the times in the tables, and the days of a report all use this time zone. The days include the rows of the **By day** table and the day groups in the **Trip and stop log**. The period options use this time zone as follows:

* **Today** starts at midnight in this time zone.
* **Yesterday** is the whole previous day, from 00:00 to 23:59.
* **This week** starts on the first day of the week for the language set in your browser.

A downloaded file names the time zone in its column headings, for example "Start (Europe/Berlin)".

In Sensor readings, an **Interval** of 1 day is a day in UTC. It starts and ends at midnight UTC, not at midnight in your time zone.

A trip that crosses midnight is one trip. It appears in full on the day when it started.

## Which trips a period includes

A report includes a trip when the trip starts inside the period. A trip that starts before the period and ends inside it isn't included. A trip that starts inside the period and ends after it is included in full, with its whole distance.

Stops follow the same rule. Under the **By asset** table, Reports states it as "A trip or a stop counts in full in the period it starts in." Under the **By day** table, it says "A trip or a stop counts in full on the day it starts on."

## Which units a report uses

Reports shows distance in kilometers and speed in km/h. On screen, a duration shows as hours and minutes, such as "2 h 05 m". In a downloaded file, a duration is a decimal number of hours, such as 1.25 for 1 hour 15 minutes. Sensor values use the unit that the GPS device reports.

## See also

* [Trips and stops](trips-and-stops.md): Learn how a trip and a stop are detected.
* [Sensor readings](sensor-readings.md): Learn how readings are grouped into 5-minute steps.
* [Manage reports](manage-reports.md): Run a report, choose the period, and download a table.
* [Troubleshooting reports](troubleshooting.md): Find out why a trip or a reading is missing from a report.
