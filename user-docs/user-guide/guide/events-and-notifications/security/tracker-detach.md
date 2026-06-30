---
description: Get alerted when a GPS tracking device is removed from the vehicle or asset it monitors. Essential for theft prevention in logistics and transportation.
---

# Tracker detach

The **Tracker detach from the object** rule alerts users when a GPS tracking device is removed from the vehicle or asset it is monitoring. The availability of this rule depends on the device's capabilities and configuration. Depending on the device's design, it may use various sensors, such as contact points or light sensors, to detect detachment.

This rule is essential for asset security and loss prevention, enabling businesses to respond quickly to potential theft, tampering, or unauthorized handling. It is especially valuable in industries like logistics and transportation, where protecting assets during transit or storage is critical.

## Rule settings

This rule is entirely dependent on the device's capabilities and configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and alerts](../README.md).

## System operation details

* **Reset timer.** The **Tracker detach from the object** alert has a 1-minute reset timer, meaning the alert doesn't trigger more often than once every minute. If an event occurs during the reset period, it is omitted from the Navixy platform, including in reports.
* **Multiple devices.** Users have the flexibility to select multiple GPS devices to receive notifications when the device is detached from an object. The only requirement is that the selected GPS devices must support **Tracker detach from the object** events and have this feature integrated into the platform.
* **GPS-independent event alert.** If the platform receives a detachment event from a GPS device without valid GPS data, the event is still counted as valid and displayed, regardless of whether it occurred inside or outside of a geofence. In such cases, the Inside/Outside settings for geofences are ignored to ensure that potentially critical events are not missed.
