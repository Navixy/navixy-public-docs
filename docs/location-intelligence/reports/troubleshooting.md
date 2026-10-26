---
description: >-
  Fix an empty report, a missing or repeated trip, a missing asset or sensor, a
  trip on the wrong day, and messages when a report doesn't run or open.
tags:
  - troubleshooting
  - beta
---

# Troubleshooting reports

This page covers the most common problems in Reports and is organized by what you see. Each entry lists what to check first, then what to do. To learn how Navixy prepares report data, see [How report data is prepared](how-report-data-works.md).

## The report is empty

Reports shows one of the following messages:

* In Trips and stops, the **Trip and stop log** shows "No trip or stop started in this period." When you select **Trips** or **Stops** for the log, it shows "No trip started in this period." or "No stop started in this period."
* Sensor readings shows "No sensor reported a reading in this period."
* Sensor readings shows "No sensor matches the filters."
* Sensor readings shows "Choose the assets whose readings to show, then Run." when you open it.
* Either report shows **No data yet** with "This report will show your fleet's data once it is available for your workspace."

Check the following, in this order:

1. **The period.** A trip or a stop belongs to the period in which it started. Choose a longer period, such as **Last week**, and click **Run**.
2. **The time of the report.** A report shows data up to the moment when you ran it. Click **Run** to run the report again and see newer data.
3. **The GPS device.** An asset without a GPS device has no trips and no readings. Assign a GPS device to the asset in [Manage assets](../bdr/assets/manage-assets.md). Data from before the assignment isn't added.
4. **The age of the data.** Navixy keeps trips for 6 months, sensor readings in 5- to 30-minute steps for 3 months, and sensor readings in 1-hour steps for 12 months. A period older than these limits has no data. For details, see [How far back report data goes](how-report-data-works.md#how-far-back-report-data-goes).
5. **The filters.** In Sensor readings, the **Sensors** and **Devices** filters can leave no rows. The report then shows "No sensor matches the filters." Click **Clear filters** to show every sensor again.

**No data yet** means that Navixy has no report data for your workspace yet. For example, no asset of the workspace has a GPS device. Assign a GPS device to an asset, and open the report again after about 30 minutes. If the message stays, contact Navixy support.

When you open Sensor readings, it shows "Choose the assets whose readings to show, then Run." The report has no assets yet. Click the button that shows the assets, choose the assets or **All assets**, then click **Run**.

The report is working when the **Trips** tile in Trips and stops, or the **Samples** tile in Sensor readings, shows a number above 0.

## A trip from today is missing

The trip is still in progress, or it ended after you ran the report. A trip appears in a report after it ends. A trip ends when the asset stands still for at least 5 minutes.

If the GPS device stopped sending data during the trip, the trip appears later. It appears when the device sends data again, or about 24 hours after the trip started. Check where the asset is now in [Tracking](../tracking/), and click **Run** to run the report again.

## A trip is missing or shorter than expected

Check the following:

* **The GPS device was offline.** Data that reaches Navixy long after the GPS device recorded it, for example after the device was offline, isn't added to trips. The trip for that period is missing or shorter. See [Late data is added to sensor readings, not to trips](how-report-data-works.md#late-data-is-added-to-sensor-readings-not-to-trips).
* **The distance was under 50 m.** A movement shorter than 50 m isn't a trip.
* **The GPS device was assigned later.** Trips from before the GPS device was assigned to the asset never appear. If you moved the GPS device from another asset, the earlier trips stay with the other asset.
* **The positions were unreliable.** Navixy doesn't use positions from a mobile network or Wi-Fi, or positions with poor satellite reception. A trip in an area with poor reception can be shorter or missing.
* **The GPS device reports rarely.** A trip ends when the GPS device sends no data for longer than the gap described in [How a trip is detected](trips-and-stops.md#how-a-trip-is-detected). A device that is set to send positions less often than that gap while moving gives split or missing trips, although it keeps sending data. When the device sends no odometer reading, too few positions also make the distance too short, because the straight lines between distant positions are shorter than the road. Shorten the reporting interval in the configuration of the GPS device, both for moving and for standing still. A shorter interval uses more mobile data on the SIM card.

## An asset is missing from the report

Check the following:

* **The asset has no GPS device.** An asset without a GPS device isn't included in any report. Assign a GPS device to the asset in [Manage assets](../bdr/assets/manage-assets.md).
* **The asset isn't chosen.** Click the button that shows the chosen assets, such as **All assets**. In the list, choose the asset or **All assets**. Then click **Run**. The search in the list of assets finds an asset by its name or ID. You can choose at most 200 assets one by one. To include more, choose **All assets**.

In Trips and stops, the **By asset** table has a row for every asset in the report, including an asset that made no trips. When the asset has a row with no trips, the asset is in the report but has no trips in the period. See [The report is empty](#the-report-is-empty).

## A trip appears twice

The asset has two GPS devices, and both devices recorded the trip. Every GPS device of an asset counts in a report, so Trips and stops shows the trip once for each device. The asset's figures, such as **Trips** and **Distance**, count the trip twice.

The **Device** column in the **Trip and stop log** shows which GPS device recorded each row. The log has this column when an asset's rows come from more than one device. In Sensor readings, each device has its own rows in the same way.

If one of the GPS devices shouldn't belong to the asset, remove it from the asset in [Manage assets](../bdr/assets/manage-assets.md). The change applies to new trips. Trips that Navixy prepared before the change stay with the asset. See [A GPS device must be assigned to the asset first](how-report-data-works.md#a-gps-device-must-be-assigned-to-the-asset-first).

## A trip appears under the wrong day

A report uses your computer's time zone. Reports names this time zone under the assets and the period at the top of the report, in the line "Times are in …". Your computer can be in a different time zone from the place where the asset drove. In that case, a trip near midnight appears under the day in your computer's time zone.

To see the days in another time zone, open the report on a computer that is set to that time zone. For details, see [Which time zone a report uses](how-report-data-works.md#which-time-zone-a-report-uses).

A trip that crosses midnight appears in full on the day when it started.

## A sensor has an unfamiliar name or no unit

Sensor readings shows each sensor under the key that the GPS device sends, such as `fuel_level` or `temperature_2`. The key comes from the GPS device, and Reports shows no other name for the sensor. To find out what a key means, check the documentation of the GPS device.

The **Unit** column shows the unit that the GPS device reports. When the GPS device reports no unit, the report shows the value without a unit. In the chart, such values are under **Readings with no unit**.

## A sensor is missing from the report

First, check that the **Sensors** and **Devices** filters in Sensor readings don't hide the sensor. Click **Clear filters** to show every sensor.

Sensor readings includes a sensor only when its GPS device sent numeric values for it in the period. Navixy can't add or configure a sensor: it stores what the GPS device sends. Check the GPS device and its installation:

* **The input.** The model of the GPS device has no such input, or the vehicle doesn't pass that CAN or OBD value to the device. Check the documentation of the GPS device and of the vehicle.
* **The device configuration.** Reading CAN or OBD data isn't enabled in the configuration of the GPS device.
* **The sensor.** The sensor has no power or is wired wrongly.

## A sensor has gaps or pauses

A gap in the chart means that this sensor had no readings for that time. The line of the chart breaks where an interval has no reading, and **Coverage** is below 100%. A gap doesn't always mean that the GPS device was off: a device can keep sending messages without this value. Navixy doesn't fill a gap with the last known value.

The [reading panel](sensor-readings.md#reading-panel) lists the long gaps as pauses, with the length of each pause. A pause is a gap more than 2.5 times as long as this sensor's usual gap between readings. To open the reading panel, click the sensor's row in the table.

The usual causes are in the configuration of the GPS device:

* The device sends the value only when it changes.
* The device reports rarely.
* The device sleeps while the ignition is off and sends only short status messages.

To get more readings, change these settings in the configuration of the GPS device.

## A sensor shows a wrong value

Navixy can't change how a sensor is read. It has no sensor settings, such as a divider or a calibration table. It shows each value as the GPS device sends it, in the device's unit. When a value looks wrong, check the following:

* The power and the wiring of the sensor, or its Bluetooth pairing with the GPS device
* The configuration of the GPS device
* For a fuel level sensor, the tilt of the tank

Some fuel level sensors send a fixed value, such as 65454 or 65455, when the sensor is disconnected or has failed. Navixy shows such a value as a normal number, and it counts in **Average**, **Min**, and **Max**. Check the documentation of the sensor for the values that it uses to report a fault.

## A report doesn't run

Reports shows one of the following messages instead of the report:

| Message | What to do |
| --- | --- |
| **This report is too large**, "Choose a shorter period or fewer assets." | Choose a shorter period or fewer assets, then click **Run**. |
| **This report is too large**, "Choose a shorter period, fewer assets or a longer interval." | This text appears in Sensor readings. Choose a shorter period, fewer assets, or a longer **Interval**, then click **Run**. |
| **This report cannot be run as chosen**, "Choose a different period or other assets." | Choose a different period or other assets, then click **Run**. |
| **Report data is unavailable**, "Report data cannot be read right now. Please try again later." | Wait a few minutes and click **Try again**. If the message appears again, contact Navixy support. |

A report that is too large doesn't run at all. Reports never shows only a part of a report.

## The chart, the map, or a download doesn't appear

Reports shows one of the following messages:

| Message | What to do |
| --- | --- |
| "The chart draws at most 8 sensors, and the filters leave N. Narrow them to see the chart." | In Sensor readings, N is the number of table rows that the filters leave. Use the **Sensors** and **Devices** filters to leave 8 rows or fewer. |
| "The chart did not load. Reload the page to see it." | Reload the page. |
| "There is no position to show on a map." | The trip panel or the stop panel has no coordinates to show. The GPS device sent no position for this trip or stop. |
| "The map did not load. Reload the page to see it." | Reload the page. |
| "This browser cannot draw the map, which needs WebGL2." | Update your browser, or open Reports in another browser. |
| "The file could not be written. Try again, or reload the page." | Download the table again. If the message appears again, reload the page. |

## You can't open Reports or a report type

Reports shows one of the following messages:

| Message | What to do |
| --- | --- |
| "The Reports product is not enabled." | Ask the people who set up your workspace to check that the workspace includes Reports and that your role includes it. |
| "You do not have the rights for this — contact your administrator." | Ask the people who set up your workspace for a role that gives access to this report type. |
| "No reports are available to you in this workspace." | Your roles give access to no report type in this workspace. Ask the people who set up your workspace for a role that gives access to Reports. |
| "This report is not available to you in this workspace." | Your role doesn't give access to this report type, for example when you open it from a link. Ask the people who set up your workspace for a role that gives access to it. |
| "You can see this report but not run it. Running it takes a role with the run permission, which is granted in Console." | Ask the people who set up your workspace for a role that lets you run this report type. |
| "Your permissions could not be read. Please contact support." | Contact Navixy support. Trying again doesn't fix this error. |
| "Your session is incomplete. Please sign in again." | Click **Sign in again**. |
| "Your session has ended. Sign in again." | Click **Sign in again**. |
| "Your account has access to no organization or workspace." | Ask the people who set up your workspace to give you a role in the workspace. |
| "This workspace asks you to sign in with a second factor, and the sign-in did not complete one." | Click **Sign out**, then sign in again and complete the second factor. |
| "Signing in is temporarily unavailable. Try again shortly." | Wait a minute and click **Try again**. If the message appears again, contact Navixy support. |
| "Something went wrong on our side. Please try again." | Wait a few minutes and click **Try again**. If the message appears again, contact Navixy support. |
| "Something went wrong. Please try again." | Click **Try again**. If the message appears again, contact Navixy support. |

A lock <picture><source srcset="../.gitbook/assets/icons/reports-lock-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-lock.svg" alt="Locked" data-size="line"></picture> next to a report type in the Reports menu doesn't mean a problem with your role. Reports shows these report types to every user, whatever the role, because Navixy offers them as paid options. See [Report types with a lock](manage-reports.md#report-types-with-a-lock) to learn how to get them.

A report type that doesn't appear in the Reports menu isn't included in your role. See [Access to Reports](./#access-to-reports) to learn how a role gives access to report types.

A change to your roles doesn't apply immediately. To apply it sooner, sign out and sign in again. See [When a change to your access applies](../access-and-roles.md#when-a-change-to-your-access-applies).
