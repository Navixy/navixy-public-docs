---
description: Detect when a vehicle-mounted GPS tracker loses external power and switches to its internal battery. Configure notifications for immediate awareness.
---

# External power cut

The **External power cut** rule is designed for wired vehicle GPS devices that depend on the vehicle’s electrical system. This rule is crucial for fleet management, as it monitors the power supply to the GPS device and alerts users when the external power is disconnected.

Upon detecting a power cut, the GPS device automatically switches to its internal battery (if available) to maintain functionality and report to the Navixy platform. The platform logs these events and notifies users according to the rule’s settings.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Reset timer.** The **External power cut** alert has a 5-minute reset timer, meaning that the alert doesn't trigger more frequently than once every 5 minutes. If an event occurs during the reset period, it is omitted from the platform and isn't included in reports.
* **Device-detected event.** This event is triggered directly by the device based on its configuration. Your device and its configuration must support this functionality to use the rule.
* **GPS-independent event alert.** The platform registers and displays the event even if it is received in a message without valid coordinates, ensuring that users are informed regardless of the event's location. The Inside/Outside settings for geofences are ignored in such cases, as the platform prioritizes displaying potentially critical events.
