---
description: Get notified when a vehicle's car alarm triggers. Detected by the GPS device's connected input, the alert is sent immediately to your monitoring dashboard.
---

# Car alarm

The **Car alarm triggered** rule is designed to enhance the security of your fleet by providing real-time notifications when a vehicle's alarm system is activated. This rule helps safeguard your assets by promptly alerting your team to potential theft, unauthorized access, or tampering incidents.

This rule works by monitoring the vehicle’s alarm system through a connected GPS tracking device. Typically, the alarm system’s output signal is wired to the input of the GPS device. When the alarm is triggered, the GPS device detects this signal and sends an alert to the Navixy platform.

## Rule settings

No specific rule settings are required. For common settings, see [Rules and alerts](../README.md).

## System operation details

- **Reset timer:** The **Car alarm triggered** alert has a 5-minute reset timer, meaning the alert doesn't trigger more than once every 5 minutes. If an event occurs during the reset period, the platform suppresses the alert, keeping notifications and reports clear and concise.
- **Multiple devices:** This rule can be applied to multiple GPS devices, as long as they support **Car alarm triggered** events and have this feature integrated into the platform. This allows you to monitor these alerts across various vehicles or devices efficiently.
- **GPS-independent event alert:** The platform processes and displays car alarm events even if the data packet lacks valid GPS coordinates. These events are recorded regardless of whether they occur inside or outside a designated geofence. The Inside/Outside geofence settings are bypassed in this case, ensuring that no critical event is missed.
