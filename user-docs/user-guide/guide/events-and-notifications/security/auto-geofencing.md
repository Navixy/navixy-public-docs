---
description: Auto-create a geofence around a parked vehicle and get alerted if it moves without the ignition on. A device-dependent anti-theft protection rule.
---

# Auto-geofencing

The **Auto geofencing** feature, available in select GPS tracking devices, is a robust solution designed to enhance vehicle security by preventing unauthorized towing or theft.

This feature automatically establishes a geofence around the vehicle's current location when the ignition is turned off. If the vehicle moves outside this predefined boundary without the ignition being engaged, the system immediately triggers an alert, providing an additional layer of protection. Utilization of the **Auto geofencing** feature requires a GPS device that supports this functionality.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and alerts](../README.md).

## System operation details

* **Reset timer**: The **Auto geofencing** alert has a 1-minute reset timer, meaning it doesn't trigger more often than once every minute. If an event occurs during the reset period, it is omitted from reports.
* **Multiple devices**: You can select multiple GPS devices for this rule, provided they support **Auto geofencing** events and the feature is integrated into the Navixy platform. This flexibility allows you to monitor several vehicles or assets simultaneously.
* **GPS-independent event alert**: If the platform receives an **Auto geofencing** event from a GPS device without valid GPS coordinates, the event is still counted as valid and displayed. This ensures that critical events are not missed, regardless of their occurrence within or outside the defined geofences.
