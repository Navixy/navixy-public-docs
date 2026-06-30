---
description: Detect distracted driving such as phone use or smoking, captured by dash cams or GPS devices with camera capabilities. Requires compatible hardware support.
---

# Distracted driving

The Distracted driving feature is a component of the advanced telematics systems, designed to detect and alert users when drivers engage in distracting activities, such as smoking or using a mobile phone.

This device-dependent feature relies on hardware configuration, typically dash cams or GPS devices with cameras, to identify and report distractions. The Navixy platform processes these events in real-time and sends notifications, enabling prompt corrective action to enhance road safety and promote responsible driving behavior.

This rule is device-dependent, meaning it relies on the configuration of the hardware (typically dash cams or GPS devices with camera capabilities) to detect and report distraction events. The platform processes these events and sends notifications to the user accordingly.

## Rule settings

Since this rule is based on hardware configuration, minimal setup is required within the platform itself. The key is ensuring that the selected devices are configured to detect and report Driver distraction events.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Reset timer:** The **Distracted driving** alert is managed by a 10-second reset timer, ensuring that alerts aren't generated more frequently than once every 10 seconds. If a distraction event occurs during the reset period, it is omitted from the platform and reports.
* **Multiple devices:** Users can configure this rule across multiple GPS devices, provided they support **Distracted driving** events and have this feature integrated into the platform. This allows for comprehensive monitoring across various vehicles or devices, ensuring consistent oversight of driver behavior.
* **GPS-independent event alert:** The platform registers and displays **Distracted driving** events even if the data packet lacks valid GPS coordinates. The Inside/Outside geofence settings are ignored in such cases, as the system prioritizes the display of potentially critical events to ensure no important information is missed.
