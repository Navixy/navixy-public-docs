---
description: Detect when an external GPS antenna is disconnected from a tracking device and get notified immediately to restore satellite signal reception.
---

# GPS antenna disconnected

The **GPS antenna disconnected** rule is designed for GPS devices that use an external antenna, typically connected via a cable and socket. When the antenna becomes disconnected (or cut off), it can disrupt satellite signal reception, leading to potential tracking issues. This rule alerts you immediately when such a disconnection occurs, allowing you to quickly address the problem and maintain continuous tracking of your assets.

For example, if you manage a fleet of delivery vehicles equipped with GPS devices and external GPS antennas, this rule helps you monitor the integrity of your GPS connections. If an antenna becomes disconnected due to environmental factors or tampering, you are immediately notified. This allows you to take corrective action, such as sending a technician to restore the connection, preventing potential delays and maintaining efficient operations.

{% hint style="info" %}
Most modern GPS devices come equipped with built-in high-sensitivity antennas, making external antennas obsolete.
{% endhint %}

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Reset timer:** The **GPS antenna disconnected** alert has a 5-minute reset timer, meaning the alert doesn't trigger more frequently than once every 5 minutes. If an event occurs during the reset period, it is omitted by the Navixy platform and isn't included in reports.
* **Multiple devices:** You can monitor multiple GPS devices with this rule, provided that the selected GPS devices support GPS antenna disconnected events and the feature is integrated into the platform. This allows for comprehensive monitoring across various vehicles or devices.
* **Event handling without coordinates:** The platform registers and displays the event even if it is received in a packet without valid GPS coordinates. This ensures that you are informed of the disconnection event, regardless of the location data. The Inside/Outside radio button settings for geofences are ignored in these cases, prioritizing the display of potentially critical events.
