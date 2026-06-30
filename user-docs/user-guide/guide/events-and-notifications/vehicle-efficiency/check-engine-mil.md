---
description: Receive an alert when the Malfunction Indicator Lamp (check engine light) activates on a vehicle. Requires a GPS device with OBD or CAN data support.
---

# Check engine (MIL)

The **Check engine (MIL)** rule is designed to monitor the status of the Malfunction Indicator Lamp (MIL), commonly known as the "Check Engine" light, in vehicles. This rule is crucial for vehicle maintenance and fleet management, as it promptly alerts users when the MIL is activated.

The MIL typically illuminates when the vehicle's onboard diagnostic system detects an issue with the engine, emissions, or other critical systems. By receiving timely notifications, users can take immediate action to address potential problems, preventing further damage and ensuring vehicle safety and compliance.

{% hint style="info" %}
To monitor specific DTC codes, you can set up a [State field value](../inputs-and-outputs/state-field-value.md) rule.
{% endhint %}

## Rule settings

This rule is entirely dependent on the device's capabilities and configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and alerts](../README.md).

## System operation details

* **Reset timer.** The **Check engine (MIL)** alert has a 24-hour reset timer, meaning it doesn't trigger more frequently than once every 24 hours. If the event occurs during the reset period, it is omitted from the Navixy platform and reports.
* **Multiple devices.** This rule can be applied to multiple GPS devices, provided they support the Check Engine (MIL) event and have the feature integrated into the platform.
* **GPS-independent event alert.** The platform logs the event and triggers notifications regardless of whether the vehicle is inside or outside a geofence, even if the data packet lacks valid GPS coordinates. The platform prioritizes capturing and displaying these critical events to ensure they are not missed.
