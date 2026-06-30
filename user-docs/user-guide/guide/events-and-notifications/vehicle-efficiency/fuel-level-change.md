---
description: Alert when the fuel sensor detects a significant level change, signaling refueling or potential fuel theft. Requires a configured fuel level sensor per device.
---

# Fuel level change

Monitoring fuel levels is crucial for managing fuel consumption and detecting unauthorized usage. The Fuel Level Change rule is designed to provide real-time notifications whenever significant changes in fuel levels occur, such as sudden increases or decreases.

These notifications can be delivered through the web user interface, SMS, or email, and can be consolidated into reports. This proactive monitoring helps users quickly respond to fuel level changes, potentially lowering fuel costs and resolving disputes among employees.

{% hint style="info" %}
Before setting up this rule, ensure that the fuel level sensor you intend to monitor is properly configured. For detailed instructions on setting up and configuring fuel level sensors, see the [Fuel level sensor section](../../devices-and-settings/vehicle-sensors/measurement-sensors/fuel-level-sensor.md).
{% endhint %}

## Rule settings

### Sensor

Specify the fuel level sensor that will be used as the source for notifications and analysis. The number of fuel changes indicated is regulated by the Accuracy parameter of the fuel level sensor, which is explained in the [Fuel level sensor section](../../devices-and-settings/vehicle-sensors/measurement-sensors/fuel-level-sensor.md).

For common settings, see [Rules and alerts](../README.md).

## System operation details

* The Navixy platform smooths sensor data and compares the total change within a 10-minute window against the fuel sensor's Accuracy setting to determine if a notification should be triggered. For example, with an Accuracy setting of 5%, a fuel level change of 5 liters or more for a 100-liter tank within 10 minutes triggers an alert. If the cumulative change meets or exceeds this threshold, the platform sends a notification.
* Fuel change volume estimation is not currently included in notifications but will be available soon.
* The **Fuel level change** alert has a 15-minute reset timer, meaning the alert doesn't trigger more often than once every 15 minutes. If a fuel level change occurs while the rule is in its reset period, the platform omits the event, including in the reports.
* This rule supports only one device per rule. This limitation is due to the complexities involved in cross-referencing multiple measurement sensors with different GPS devices, calibration tables, and other measurement and filtering aspects.
