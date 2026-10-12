---
description: >-
  When new trips and sensor readings appear in Reports, how long report data is
  kept, what happens to late data, and which GPS device and time zone apply.
tags:
  - fundamentals
  - beta
---

# How report data is prepared

Reports doesn't read your GPS devices directly. Navixy prepares trips, stops, and sensor readings from device data and stores them. When you generate a report, Navixy combines the stored data with data calculated from the newest device data. This page explains when data appears, how long it is kept, and which data a report includes. These rules apply to every report type, except where a section names one.

## When new data appears in a report

A report shows trips, stops, and sensor readings up to the moment when you generate it. To see newer data, [generate the report again](manage-reports.md#generate-a-report).

Navixy adds a trip to report data only after the trip ends. A trip ends when the asset stands still for at least 5 minutes. As a result, a trip that is in progress isn't in any report.

When the GPS device sends no data for more than 15 minutes, the trip ends at the last position before the gap. Navixy adds this trip when the GPS device sends data again, or about 24 hours after the trip started. To see where an asset is now, use [Tracking](../tracking/).

## How far back report data goes

Navixy keeps each kind of report data for a fixed time. Sensor readings are kept in steps: the values of each sensor grouped over a few minutes. [Sensor readings](sensor-readings.md#how-readings-are-grouped-into-5-minute-steps) explains how steps work.

| Data | Kept for |
| --- | --- |
| Trips and stops | 6 months |
| Sensor readings, in 5-minute to 30-minute steps | 3 months |
| Sensor readings, in 1-hour steps | 12 months |
| Visits to geo objects | 3 months |

A report has no data for a period that is older than the time shown in the table. For sensor readings from 3 to 12 months ago, Navixy keeps only the 1-hour steps.

A report can cover a period of up to 31 days. Report data starts when your workspace was created.

## Data that arrives late isn't added

Navixy doesn't change report data after it stores the data. Data that reaches Navixy long after the GPS device recorded it is therefore left out of reports. This is common when a GPS device was offline and sends its stored data when it connects again.

A position that arrives after a newer position from the same GPS device is also left out of trips. As a result, for a period when a GPS device was offline, a report can miss a trip or show a shorter trip.

## A GPS device must be assigned to the asset first

A report includes only the trips and readings that Navixy prepared while the GPS device was assigned to an asset. Data from before the assignment isn't added, even after you assign the device.

When you move a GPS device to another asset, the earlier trips and readings stay with the old asset. New trips and readings go to the new asset. The change applies within about 30 minutes.

When an asset has more than one GPS device, trips, stops, and visits to geo objects use only its primary device. The primary device is the device that gives the asset its position, as set in [Manage assets](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset). Sensor readings shows the readings of each GPS device of the asset.

## Which time zone a report uses

A report uses the time zone from your account settings. The times in the table, the day headings, and the period that you choose all use this time zone. For example, **Today** starts at midnight in this time zone, and **Yesterday** is the whole previous day, from 00:00 to 23:59.

A trip that crosses midnight is one trip. It appears in full on the day when it started.

## Which trips a period includes

A report includes a trip when the trip starts inside the period. A trip that starts before the period and ends inside it isn't included. A trip that starts inside the period and ends after it is included in full, with its whole distance.

## Which units a report uses

Reports shows distance in kilometers, speed in km/h, and duration in hours and minutes. Sensor values use the unit that the GPS device reports.

## See also

* [Trips & Stops](trips-and-stops.md): Learn how a trip and a stop are detected.
* [Sensor readings](sensor-readings.md): Learn how readings are grouped into 5-minute steps.
* [Troubleshooting reports](troubleshooting.md): Find out why a trip or a reading is missing from a report.
