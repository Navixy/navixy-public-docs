---
description: Set up fuel level alerts in Navixy with customizable thresholds, geofencing conditions, and notifications for refills and drain events.
---

# Fuel level alerts and notifications

Before setting up the **Fuel level change** rule, make sure to add and configure the fuel level sensor users intend to monitor. For more comprehensive instructions on how to do this, consult the [Measurement sensor](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/vehicle-sensors/measurement-sensors) article.

## Description

The purpose of this rule is to facilitate real-time monitoring of the fuel level by promptly notifying about any significant changes, whether they are decreases or increases. Notifications can be sent via web user interface, SMS, and/or email, and can also be consolidated into reports. By detecting fuel level changes, this feature enables users to remain connected with the device and respond more swiftly than relying solely on the fuel level report. Which, of course, can lower fuel costs and resolve labor disputes among employees.

## Settings

**Sensor:** Specify a previously created fuel level sensor as a source for notifications and analysis.

![](../../../../.gitbook/assets/fuel-alert-settings-1.png)

The number of fuel changes to be indicated is regulated and filtered by the Accuracy parameter of the fuel level sensor in the **Sensors and buttons** block.

![](../../../../.gitbook/assets/fuel-alert-settings-2.png)

In the screenshot above, according to the calibration table, the maximum fuel tank is 150 L. This is the value that **Accuracy** calculates the percentage of. If no calibration is provided, then it will be an absolute value of 100 units (in this case, would be 100 Litres). Based on the resulting of accuracy percentage value in selected units, the platform filters the incoming changes from this sensor and compares their sum within 10 minutes with the Accuracy resulting parameter.

{% hint style="info" %}
Rules for draining and refueling are based on the calibration table and absolute error, which is calculated as `tank volume * accuracy`. The platform will record the last current reading of the sensor for a ten-minute span. Based on this reading, the following events will be triggered:

- If the fuel level has increased by more than the absolute error, a "filling" event will be recorded.
- If the fuel level has decreased by more than the absolute error, a "draining" event will be recorded. For instance, if the tank capacity is 100 liters and the accuracy is 5%, a 5-liter change in fuel level within a 10-minute timeframe will trigger the rule.
  {% endhint %}

10% from 150 L = 15 L is resulting, the minimum amount of fuel that can be decreased or filled to the tank before triggering a fuel level change notification. If the fuel level change within a 10-minute period is solely 15L or greater, or if the total fuel level change within that period is equal to or greater than the **Accuracy** value, the user will receive a notification.

![](../../../../.gitbook/assets/fuel-alert-settings-3.png)

23 L delta will trigger the notification, meaning the 23 L difference between 2 adjacent points is greater than the 15 L in the Accuracy resulting value.

![](../../../../.gitbook/assets/fuel-alert-settings-4.png)

Total change within 10 minutes. Here is another case when users can receive the notification. In sum, there is approximately 140+ L of fuel change within a 10 minutes period which is much greater than 15 L of the resulting **Accuracy** value.

**Geofence:**

Fill out the section if the rule needs to work only inside/outside of the selected geofences.

**Bind zone to rule:**

Enables geofence rule binding.

**Map button:**

Shows bound geofences on the map.

## The platform specifics

- The **Fuel level change** alert has a 15-minute reset timer, meaning the alert event will not occur more often than once every 15 minutes. If this type of event occurs in time the rule has been waiting for the reset, this event will be omitted by the platform, including the reports.
- The rule supports only one device per rule because multiple different sources of measurement sensors can not be cross-referenced with multiple devices, calibration tables and other aspects of measuring and filtering data.

## Notifications

**Emergency notification:**

is used for important events. A message on the screen and the sound signal can only be disabled by clicking on the notification. Please note, some browsers can block notification sound until user activity is recorded on the page.

**Push notifications:**

Receive push notifications on the mobile app and web interface.

**Add geofence name to the notification:**

Adds names of the specified geofences to the notification text. This option is available only when the **Inside** geofence binding radio button is selected on the **Settings** tab.

**SMS notifications:**

List of recipients for SMS notifications when the event occurs.

**Email notifications:**

List of recipients for email notifications when the event occurs.

## Schedule

Set a schedule for when the rule runs. If your schedule indicates that the event shouldn't run some day or time period, it won't appear as a notification in the user interface, and notifications via SMS or email will not be sent. Additionally, you can choose a default template for quick scheduling.

## Event reports

To view the dates when the events were received, you can build a [Report on all events](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/report-on-all-events).

![](../../../../.gitbook/assets/fuel-alert-event-report-1.png)

![](../../../../.gitbook/assets/fuel-alert-event-report-2.png)
