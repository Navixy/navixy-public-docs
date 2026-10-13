---
description: >-
  Sensor readings: which GPS device values the report includes, how they're
  grouped into intervals, how each figure is calculated, and how pauses are found.
tags:
  - reference
  - beta
---

# Sensor readings

**Sensor readings** shows the values that the GPS devices of your assets sent over a period. The values include fuel level, temperature, battery voltage, and engine speed. Use it to check how a value changed, to find the highest temperature in a refrigerated trailer, or to see when a device stopped sending data.

This page describes what the report shows and how its values are calculated. To run the report, see [Manage reports](manage-reports.md#run-a-report).

{% hint style="info" %}
**Access**

**Sensor readings** is in your Reports menu when your role gives access to it. See [Access to Reports](README.md#access-to-reports).
{% endhint %}

## What the report shows

<figure><img src="../.gitbook/assets/reports-sensor-readings.png" alt="The Sensor readings report for one day, filtered to two sensors with the Sensors filter. Under the period, the Interval options run from 5 min to 1 day. Four tiles show 1 asset, 1 device, 2 sensors, and the number of samples. A chart titled Readings with no unit shows one line for each sensor, starting late in the morning, when the readings start. The table lists the two sensors under the keys that the device sends, with Average, Min, Max, First, Last, and Change."><figcaption><p>Sensor readings for two sensors: the interval options, the tiles, a chart of the readings, and the table of sensors</p></figcaption></figure>

The report opens with no assets chosen.

Here's what you can do in the report:

* Choose the assets and the period, and click **Run**.
* Read the counts in the **Assets**, **Devices**, **Sensors**, and **Samples** tiles.
* Compare each sensor's figures for the period: **Average**, **Min**, **Max**, **First**, **Last**, **Change**, **Samples**, and **Coverage**.
* See a chart of the readings for each unit.
* Narrow the table with the **Sensors** and **Devices** filters.
* Click a row to open its reading panel and see when the readings paused.
* Change the **Interval** that the chart and the pauses use.
* Hide columns and download the table. See [Show and hide columns](manage-reports.md#show-and-hide-columns) and [Download a table](manage-reports.md#download-a-table).

The download menu of this report offers two tables, each as **CSV** or **Excel (XLSX)**. **By sensor** is the table as it's shown. **Readings by interval** has one row for each interval of each sensor.

## Which values the report includes

The report includes every numeric value that a GPS device sends. Each value appears under the key that the GPS device sends for it, such as `fuel_level` or `lls_level_0`. The report also includes the battery level, the battery voltage, the board voltage, and the odometer. Values that are only 0 or 1 are included too, except digital inputs and outputs.

The report doesn't include the following:

* GPS values: Speed, satellites, and altitude
* Digital inputs and outputs, such as a door sensor connected to an input
* Text values and fault codes

Sensor values don't depend on the position. The report includes the values from every message, including a message without a position or with a position that trips don't use. For this reason, the report can have values for a period in which [Trips and stops](trips-and-stops.md) has no trip.

Values appear as the GPS device sends them. Reports doesn't convert or calibrate them, so a fuel sensor that sends a raw number shows that number, not liters. The unit is the unit that the device reports, and it appears in the **Unit** column. When a GPS device reports no unit, the report uses these units:

* Battery level: %
* Battery voltage and board voltage: mV
* Odometer: meters

The unit and the type of the value are part of what makes a row, so one GPS device can show the same value in two rows. For example, a firmware update can change the unit or the type of a value.

## How readings are grouped into 5-minute steps

Navixy groups the values of each sensor into steps of 5 minutes. For each step, Navixy keeps six figures: the lowest value, the highest value, the first value, the last value, the sum of all values, and the number of readings. A report calculates its values from these steps, not from every message that a GPS device sends.

A reading at 10:07 belongs to the step that starts at 10:05. A short peak and a value that stayed high for the whole 5 minutes give the same highest value.

The report then combines these steps into the [interval](#interval) that you choose. For example, a 30-minute interval combines six steps.

## Interval

**Interval** sets the length of the time intervals that the chart, the pauses, the **Coverage** column, and the **Readings by interval** table use. The options are 5 min, 10 min, 30 min, 1 hr, 4 hr, and 1 day.

The report offers only the intervals that split the period into at most 2,016 intervals. For example, the report offers 5 min for a period of up to 7 days and 10 min for a period of up to 14 days. For a longer period, the shortest interval is 30 min.

Until you choose an interval, the report uses the shortest interval that splits the period into at most 800 intervals:

| Period | Interval |
| --- | --- |
| Up to 2 days | 5 min |
| Up to 5 days | 10 min |
| Up to 16 days | 30 min |
| Up to 31 days | 1 hr |

When you choose another interval, the report runs again. The interval that you chose stays when you run the report for another period, as long as that period offers it.

A 1-day interval runs from midnight to midnight UTC. For this reason, a 1-day interval can start and end at a different time than your local day.

## How the values are calculated

The table shows one row for each sensor of each GPS device. The values of a row cover the whole period:

| Column | How it's calculated |
| --- | --- |
| **Average** | The sum of all readings in the period divided by the number of readings, so each reading counts the same |
| **Min** | The lowest value in the period |
| **Max** | The highest value in the period |
| **First** | The first value in the period |
| **Last** | The last value in the period |
| **Change** | **Last** minus **First**, with its sign |
| **Samples** | The number of readings in the period |
| **Coverage** | The share of the period's intervals that have at least one reading |

For a value that is only 0 or 1, such as an on or off state, **Average** is the share of readings that were 1.

For a sensor that counts up, such as mileage, **Change** shows how much the counter grew over the period. When the counter was reset during the period, **Change** is smaller than the real growth.

**Coverage** leaves out the intervals that haven't started yet. The interval in progress when you run the report counts only when it already has a reading. **Coverage** is rounded down to a whole percent. A coverage above 0 and under 1% shows as <1%.

Values are rounded to at most 2 decimals and are in the unit that the device reports.

## Table columns

The table has these columns, all shown by default: **Asset**, **Device**, **Sensor**, **Unit**, **Average**, **Min**, **Max**, **First**, **Last**, **Change**, **Samples**, and **Coverage**.

* **Device** appears when the sensors of an asset come from more than one GPS device.
* **Sensor** always shows. You can hide every other column.

A sensor that sent no reading in the period has no row.

The **Sensors** tile counts rows, not sensor names. A sensor that two GPS devices send counts twice, and so does a sensor that appears with two units or two value types.

## Filters

The **Sensors** and **Devices** filters narrow the table, the tiles, and the chart to the sensors or GPS devices that you choose. The filters don't run the report again.

* **Sensors** lists the sensors in the report.
* **Devices** lists the GPS devices, grouped by asset. It appears when the readings of an asset come from more than one GPS device.

To show every row again, click **Clear filters**.

When the filters leave no row, the report says so. See [Troubleshooting reports](troubleshooting.md).

## Chart

The report draws one chart for each unit, one under the other. Sensors that have the same unit share a chart.

The chart draws at most 8 sensors. When the filters leave more than 8 sensors, the report asks you to narrow the filters, and no chart appears.

Each line shows the average for each interval. A shaded band around the line runs from the lowest to the highest value in each interval. A break in a line is an interval without a reading. Navixy doesn't fill such an interval with an estimated value. When an interval has a reading and the intervals on both sides have none, the chart shows that reading as a dot.

Point to a chart to see the values at that time, on all charts at once.

The color next to the sensor in the table row is the color of its line.

## Reading panel

Click a row to open its reading panel. The panel shows the following:

* The sensor, the asset, the GPS device, and the unit
* All eight figures of the row, from **Average** to **Coverage**
* A chart of the readings, with the pauses shaded
* **Pauses**: The number of pauses and their total length, then each pause with its start, its end, and its length

A pause is a gap in the readings that is more than 2.5 times as long as the sensor's usual gap. The usual gap depends on how often the device sends this value. For example, a sensor with a reading in every interval pauses when two intervals in a row have no reading. A sensor that sends a reading every third interval doesn't pause between its readings.

The report measures pauses in whole intervals of the **Interval** that you chose. A gap at the start or the end of the period counts too. For example, a device stops sending on the second day of the period. The panel then shows one pause, from that time to the end of the period or to the time that you ran the report.

## See also

* [How report data is prepared](how-report-data-works.md): Learn when a reading appears in a report and how long readings are kept.
* [Manage reports](manage-reports.md): Run the report, share it by its link, and download its tables.
* [Troubleshooting reports](troubleshooting.md): Find out why a report is empty, a sensor is missing, or its readings have gaps.
