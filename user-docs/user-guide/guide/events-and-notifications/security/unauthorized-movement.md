---
description: Get alerted when a stationary vehicle starts moving without authorization. Uses the GPS tracker's accelerometer to detect potential theft or unauthorized use.
---

# Unauthorized movement

The **Unauthorized movement** rule is designed to enhance vehicle security by alerting users when a stationary vehicle begins moving without authorization. This rule is particularly valuable in situations where the vehicle is expected to remain stationary, such as when it is turned off, yet unexpectedly starts moving. It helps in quickly identifying potential theft or unauthorized use, enabling users to take swift action.

**Typical applications:**

- **Theft prevention:** Immediate alerts if a vehicle is moved without authorization.
- **Parking lot security:** Monitoring vehicles in designated parking areas for unauthorized movement.
- **Asset protection:** Ensuring that stationary vehicles or equipment remain secure during off-hours.

The rule works by using the GPS device's built-in accelerometer and other configuration settings to detect any unauthorized movement. The accuracy of this rule depends on proper device installation and configuration, which varies by device model. Once the Navixy platform receives the data packet indicating unauthorized movement, it processes and displays the event, providing users with real-time alerts.

## Rule settings

This rule is entirely dependent on the device’s capabilities and hardware configuration. Typically, the device must be able to detect when the vehicle is not in use, such as by monitoring the ignition state.

There are no specific settings to configure within the rule itself. For common settings, see [Rules and alerts](../README.md).

## System operation details

- **Reset timer:** The **Unauthorized movement** alert has a 5-minute reset timer, meaning the alert doesn't trigger more frequently than once every 5 minutes. If an event occurs while the rule is in the reset period, the platform suppresses the alert, ensuring that notifications and reports remain clear and manageable.
- **Multiple devices:** This rule can be applied to multiple GPS devices, provided they support **Unauthorized movement** events and have this feature integrated into the platform. This allows users to monitor unauthorized movement across various vehicles, ensuring comprehensive security coverage.
- **GPS-independent event processing:** The platform processes and displays unauthorized movement events even if the data packet lacks valid GPS coordinates. These events are recorded regardless of whether they occur inside or outside a designated geofence. The Inside/Outside geofence settings are bypassed in this case, ensuring that no critical event is missed.
