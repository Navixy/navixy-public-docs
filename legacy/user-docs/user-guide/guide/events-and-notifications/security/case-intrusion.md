---
description: Get alerted when the GPS tracker's case is opened or tampered with. Protects valuable cargo by detecting unauthorized access to the tracking device itself.
---

# Case intrusion

The **Case intrusion** rule is designed for GPS devices that support an alert when the device's case is tampered with or opened, ensuring that you are immediately notified of any unauthorized access to your devices.

This rule is vital for enhancing the security and protection of valuable cargo during transit or storage. By using GPS devices equipped with the **Case intrusion** rule, you can add an extra layer of security to safeguard your assets.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and alerts](../README.md).

## System operation details

* **Reset timer.** The **Case intrusion** alert has a 1-minute reset timer, meaning the alert doesn't trigger more often than once every minute. If an event occurs during the reset period, it is omitted from the Navixy platform, including in reports.
* **Multiple devices.** You can select multiple GPS devices that trigger notifications when a tampering event occurs. The only requirement is that the selected GPS devices must support Tamper Detection events and have this feature integrated into the platform.
* **GPS-independent event alert.** This rule operates independently of GPS coordinates. If the platform receives a tampering event from a GPS device without valid GPS data, the event is still counted as valid and displayed, regardless of whether it occurred inside or outside of a geofence. In such cases, the Inside/Outside radio button settings for geofences are ignored to ensure that potentially critical events are not missed.
