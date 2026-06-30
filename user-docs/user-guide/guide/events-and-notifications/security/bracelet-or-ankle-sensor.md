---
description: Receive immediate alerts when a GPS bracelet or ankle sensor is removed without authorization. Designed for legal monitoring applications such as house arrest.
---

# Bracelet or ankle sensor

The **Bracelet sensor** (or **Ankle sensor**) rule is specifically designed for legal monitoring applications involving specialized GPS devices. It is an essential tool for security in contexts such as house arrest or parole.

This rule is designed to immediately notify security personnel if a GPS device is removed without authorization. In these situations, the rule triggers an instant alert to the relevant authorities, allowing for quick action to prevent any unauthorized movement or breach of legal conditions.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and alerts](../README.md).

## System operation details

* **Reset timer.** The **Bracelet sensor** alert has a 3-minute reset timer, meaning the alert event doesn't occur more often than once every 3 minutes. If this type of event occurs in time the rule has been waiting for the reset, this event is omitted by the Navixy platform, including the reports.
* **Multiple devices:** With this rule, you have the flexibility to select multiple GPS devices for receiving notifications. The only requirement is that the selected GPS devices must support Bracelet sensor events and have this feature integrated into the platform. This allows you to monitor multiple compatible GPS devices simultaneously, ensuring you receive notifications for the relevant events across various vehicles or devices efficiently.
* **GPS-independent event alert:** When the platform detects this type of hardware event from GPS device data that lacks valid coordinates, it still registers and displays the event as valid. This applies regardless of whether the event took place inside or outside of the designated geofences. In such cases, the platform disregards the Inside/Outside geofence logic to ensure the event is captured and reported.
