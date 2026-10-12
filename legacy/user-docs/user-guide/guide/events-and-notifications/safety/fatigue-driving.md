---
description: Detect signs of driver fatigue using tracker cameras and send dispatch alerts immediately when drowsiness is identified, enabling preventive action.
---

# Fatigue driving

The **Fatigue driving** rule is a critical feature within the telematics platform, designed to enhance road safety by detecting and alerting dispatch teams when signs of driver fatigue are identified. Using advanced GPS device cameras, the system continuously monitors drivers for indicators of drowsiness or fatigue, which are key contributors to road accidents.

When fatigue is detected, an immediate alert is sent to dispatch, allowing for prompt intervention, such as arranging rest breaks or providing necessary support. Implementing this rule not only helps in preventing accidents and reducing operational losses but also prioritizes driver well-being and promotes safe driving practices.

## Rule settings

Since this rule is hardware-dependent, configuration is minimal within the Navixy platform itself. The primary requirement is ensuring that the selected GPS devices are equipped to support and detect Fatigue Driving events.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

- **Reset timer:** The **Fatigue driving** alert is governed by a 5-second reset timer, ensuring that alerts don't trigger more frequently than once every 5 seconds. If a fatigue event is detected during this reset period, it is omitted from the platform and reports.
- **Multiple devices:** This rule allows the selection of multiple device for monitoring. The selected devices must support Fatigue Driving events and have this feature integrated into the platform. This enables comprehensive monitoring across various vehicles or devices, ensuring that driver fatigue is consistently tracked and managed.
- **GPS-independent event alert:** The platform registers and displays Fatigue Driving events even if the data packet lacks valid GPS coordinates. In such cases, the Inside/Outside geofence settings are ignored, as the system prioritizes the display of critical events, ensuring that no crucial information is overlooked.
