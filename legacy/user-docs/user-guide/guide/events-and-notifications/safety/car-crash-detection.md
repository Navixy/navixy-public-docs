---
description: Detect vehicle collisions using the GPS tracker's accelerometer and receive immediate alerts. Requires proper device installation with correct axis alignment.
---

# Car crash detection

The **Car crash detection** rule monitors and alerts you to significant driving events by specifically detecting vehicle collisions. This rule helps maintain awareness of driver behavior and enables your dispatch team to respond swiftly to any accidents.

This rule is hardware-dependent, triggered by accelerometer data from the GPS device. The event is generated on the GPS device side, and the platform displays the alert based on the data received.

Accurate collision detection requires proper installation of the GPS device, including correct wiring and alignment along the x, y, and z axes. Detailed installation instructions are typically provided in the GPS device’s user manual.

## Rule settings

As this rule is processed on the GPS device side rather than in the cloud, no additional settings are required.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- **Reset timer:** The **Car crash detection** alert has a 1-minute reset timer, meaning the alert doesn't trigger more frequently than once per minute. If another collision event is detected within this reset period, it is omitted from the platform and reports.
- **Multiple devices:** This rule allows you to select multiple GPS devices for notifications. The only requirement is that the GPS devices must support Car Crash Detection events and have this feature integrated into the platform. This enables monitoring of collision events across various vehicles.
- **GPS-independent event alert:** The platform registers and displays a **Car crash detection** event even if the data packet lacks valid GPS coordinates. The Navixy platform prioritizes displaying critical events, regardless of whether they occurred inside or outside designated geofences. The **Inside/Outside geofence** settings are ignored for this rule to ensure that no crucial event is missed..
