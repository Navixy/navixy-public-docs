---
description: >-
  Fix an empty report, a missing trip, asset, or sensor, a trip on the wrong day,
  wrong sensor values, and errors when a report doesn't generate or open.
tags:
  - troubleshooting
  - beta
---

# Troubleshooting reports

This page covers the most common problems in Reports and is organized by what you see. Each entry lists what to check first, then what to do. To learn how Navixy prepares report data, see [How report data is prepared](how-report-data-works.md).

## The report is empty

**Trips & Stops** shows "No trips for the period. Relax the filters or extend the period." **Sensor readings** shows "Filters hid all rows — broaden sensors, assets or period."

Check the following, in this order:

1. **The period.** A trip belongs to the period in which it started. Choose a longer period, such as **7 days**, and generate the report again.
2. **The time of the report.** A report shows data up to the moment when you generated it. Generate the report again to see newer data.
3. **The GPS device.** An asset without a GPS device isn't included in any report. For such an asset, **Sensor readings** shows "This asset has no trackers attached — nothing to read." Assign a GPS device to the asset in [Manage assets](../bdr/assets/manage-assets.md).
4. **The age of the data.** Navixy keeps trips for 6 months, sensor readings in 5- to 30-minute steps for 3 months, and sensor readings in 1-hour steps for 12 months. A period older than that has no data. For details, see [How far back report data goes](how-report-data-works.md#how-far-back-report-data-goes).
5. **The sensors.** In **Sensor readings**, check that **Devices** and **Sensors** aren't empty. The report shows only the sensors that you select.

The report is working when **Trips** in Trips & Stops, or **Total readings** in Sensor readings, shows a number above 0.

## A trip from today is missing

The trip is still in progress, or it ended after you generated the report. A trip appears in a report after it ends. A trip ends when the asset stands still for at least 5 minutes.

If the GPS device stopped sending data during the trip, the trip appears when the device sends data again, or about 24 hours after the trip started. Check where the asset is now in [Tracking](../tracking/) and generate the report again.

## A trip is missing or shorter than expected

Check the following:

* **The GPS device was offline.** Data that reaches Navixy long after the GPS device recorded it, for example after the device was offline, isn't added to trips. The trip for that period is missing or shorter. See [Data that arrives late isn't added](how-report-data-works.md#data-that-arrives-late-isnt-added).
* **The distance was under 50 m.** A movement shorter than 50 m isn't a trip.
* **The GPS device was assigned later.** Trips from before the GPS device was assigned to the asset never appear. If you moved the GPS device from another asset, the earlier trips are in the report of that asset.
* **The positions were unreliable.** Navixy doesn't use positions from a mobile network or Wi-Fi, or positions with poor satellite reception. A trip in an area with poor reception can be shorter or missing.
* **The GPS device reports rarely.** A trip ends when the GPS device sends no data for longer than the gap described in [How a trip is detected](trips-and-stops.md#how-a-trip-is-detected). A device that is set to send positions less often than that while moving gives split or missing trips, although it keeps sending data. When the device sends no odometer reading, too few positions also make the distance too short, because the route cuts corners. Shorten the reporting interval in the configuration of the GPS device, both for moving and for standing still. A shorter interval uses more mobile data on the SIM card.

## An asset is missing from the report

Check the following:

* **The asset has no GPS device.** Assign a GPS device to the asset in [Manage assets](../bdr/assets/manage-assets.md).
* **The asset isn't selected.** Check **Asset types** and **Assets** in the report settings, then click **Generate**.

## A trip appears under the wrong day

A report uses the time zone from your account settings. Your account can have a different time zone from the place where the asset drove. In that case, a trip near midnight appears under the day in the account time zone. Check the time zone in your account settings.

A trip that crosses midnight appears in full on the day when it started.

## The address columns are empty

**Start address** and **End address** show the name of a geo object, not a street address. The cell is empty when the trip started or ended outside every geo object. To see a name, create a geo object for that location in [Create geo object](../bdr/geo-objects/create-geo-object.md). A new geo object appears in reports within about 30 minutes.

The Trips dashboard in Dashboards can show a street address for the same trip. Reports shows geo object names only.

The address columns use only geo objects drawn as an area or a circle. A geo object drawn as a point or a line doesn't fill them.

## A sensor has an unfamiliar name or no unit

**Sensor readings** shows each value under its sensor label. A value that has no label appears under the name that the GPS device uses, such as `lls_level_0` or `temperature_2`. To find out what such a name means, check the documentation of the GPS device.

The report shows the unit that the GPS device reports. When the GPS device reports no unit, the report shows the value without a unit.

Each row shows the unit of its sensor. The **Total** row shows no unit when the rows have different units.

## A sensor is missing from the report

**Sensor readings** includes a sensor only when its GPS device sent numeric values for it. Navixy can't add or configure a sensor: it stores what the GPS device sends. Check the GPS device and its installation:

* **The input.** The model of the GPS device has no such input, or the vehicle doesn't pass that CAN or OBD value to the device. Check the documentation of the GPS device and of the vehicle.
* **The device configuration.** Reading CAN or OBD data isn't enabled in the configuration of the GPS device.
* **The sensor.** The sensor has no power or is wired wrongly.

## A sensor has gaps or "No data" rows

A gap in the chart, or a row that starts with "No data", means that this sensor had no readings for that time. It doesn't always mean that the GPS device was off: a device can keep sending messages without this value. Navixy doesn't fill a gap with the last known value.

The usual causes are in the configuration of the GPS device:

* The device sends the value only when it changes.
* The device reports rarely.
* The device sleeps while the ignition is off and sends only short status messages.

To get more readings, change these settings in the configuration of the GPS device.

## A sensor shows a wrong value

Navixy can't change how a sensor is read. It has no sensor settings, such as a divider or a calibration table, and it shows each value as the GPS device sends it, in the unit of the device. When a value looks wrong, check the following:

* The power and the wiring of the sensor, or its Bluetooth pairing with the GPS device
* The configuration of the GPS device
* For a fuel level sensor, the tilt of the tank

Some fuel level sensors send a fixed value, such as 65454 or 65455, when the sensor is disconnected or has failed. Navixy shows such a value as a normal number, and it counts in **Min**, **Max**, and **Avg**. Check the documentation of the sensor for the values that it uses to report a fault.

## A report doesn't generate

Reports shows one of the following messages:

| Message | What to do |
| --- | --- |
| "This report is too large. Choose a shorter period or fewer assets." | Choose a shorter period or fewer assets, then generate the report again. |
| "Report data cannot be read right now. Please try again later." | Wait a few minutes and try again. If the message appears again, contact Navixy support. |

## You can't open Reports or a report type

Reports shows one of the following messages:

| Message                                                             | What to do                                                                                                                                                                                                           |
| ------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "The Reports product is not enabled."                               | Ask the people who set up your workspace to check that the workspace includes Reports and that your role includes it.                                                                |
| "You do not have the rights for this — contact your administrator." | Ask the people who set up your workspace for a role that gives access to this report type.                                                                                                                                             |
| "Your permissions could not be read. Please contact support."       | Contact Navixy support. Trying again doesn't fix this error.                                                                                                                                                         |
| "Your session is incomplete. Please sign in again."                 | Sign out and sign in again.                                                                                                                                                                                          |
| "Your session has ended. Sign in again."                            | Sign in again.                                                                                                                                                                                                       |
| "Your account has access to no organization or workspace."          | Ask the people who set up your workspace to give you a role in the workspace.                                                                                                                                                |
| "Something went wrong on our side. Please try again."               | Wait a few minutes and try again. If the message appears again, contact Navixy support.                                                                                                                              |
| "This report template does not exist in your workspace."            | Your role gives access to the report type, but the report type isn't set up in this workspace. Ask the people who set up your workspace to check that the workspace includes Reports. Adding Reports to a workspace sets up the report types. |

A report type that doesn't appear in the Reports menu isn't included in your role. See [Access to Reports](./#access-to-reports) to learn how a role gives access to report types.

A change to your roles doesn't apply at once. To apply it sooner, sign out and sign in again. See [When a change to your access applies](../access-and-roles.md#when-a-change-to-your-access-applies).
