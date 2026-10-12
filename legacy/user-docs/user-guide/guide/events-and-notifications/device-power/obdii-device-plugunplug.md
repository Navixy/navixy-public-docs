---
description: Get alerted when an OBD GPS tracker is plugged in or disconnected from the vehicle's OBD port, helping you prevent tracking gaps from unauthorized removal.
---

# OBDII device plug/unplug

The **OBDII Device Plug / Unplug** rule is designed to provide immediate alerts whenever an OBDII GPS device is connected or disconnected from the vehicle’s OBDII port. This rule ensures that users can take prompt action to maintain continuous tracking and device functionality.

For instance, when the GPS device is unplugged, it switches to its internal battery, which has a limited lifespan. Immediate notifications upon disconnection allow users to respond quickly, ensuring uninterrupted tracking and data transmission.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Reset timer.** The **OBDII Device Plug/Unplug** alert has a 5-minute reset timer, meaning the alert doesn't trigger more frequently than once every 5 minutes. If the event occurs while the rule is in the reset period, it is omitted by the Navixy platform, including in reports.
* **Multiple devices.** **Multiple devices:** Users can select multiple GPS devices to monitor under this rule. The only requirement is that the selected devices must support OBDII port plug / unplug events. This flexibility allows users to monitor this event type across various vehicles or devices conveniently.
* **GPS-independent event alert.** The system processes these events regardless of whether GPS data is available. The event is still logged and displayed, even if it occurs outside defined geofences.
