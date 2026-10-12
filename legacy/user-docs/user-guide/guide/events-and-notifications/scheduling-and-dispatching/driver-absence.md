---
description: Detect when a driver is absent from their seat while the vehicle is in operation, using AI-equipped cameras. Alerts fleet managers to potential safety risks.
---

# Driver absence

## Overview

The **Driver absence** event is designed to monitor and ensure the presence of the driver in the vehicle's seat. This feature is particularly important for preventing the vehicle from being left unattended in situations where it could pose a security risk or violate company policies.

### How it works

This feature typically relies on in-vehicle video systems equipped with AI and sensors to continuously monitor the driver's seat. These systems use a combination of visual recognition (via cameras) and sensor data to detect the presence or absence of a driver. If the system detects that the driver's seat is unoccupied when the vehicle is in operation or when it should be attended, it triggers a **Driver absence** event.

This event is then communicated to the Navixy telematics platform, which can generate alerts, record the event, and notify the appropriate personnel. An alert might be triggered, for example, if the vehicle is in motion and the system detects that there is no driver in the seat, or if the driver leaves the seat without properly securing the vehicle.

### Applications

- **Fleet safety:** Ensuring that vehicles are not operated without a driver, preventing accidents and unauthorized use.
- **Security:** Alerting fleet managers if a vehicle is left unattended in a potentially dangerous or high-risk area.
- **Compliance:** Helping fleets comply with safety regulations that require a driver to be present during vehicle operation.
- **Insurance:** Providing evidence in case of incidents where driver absence could be a factor, which may be required for insurance claims or legal purposes.

The **Driver absence** event is a critical component in enhancing fleet safety and security, providing fleet managers with the tools needed to monitor and respond to situations where driver presence is required.

## Rule settings

No specific settings are required for this rule.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- **Reset timer:** The **Driver absence** alert has a 10-second reset timer, meaning the alert doesn't trigger more frequently than once every 10 seconds. If an event occurs while the rule is in the reset period, the Navixy platform suppresses the alert, keeping notifications and reports clear and manageable.
- **Multiple devices:** This rule can be applied to multiple GPS devices, as long as they support **Driver absence** events and have this feature integrated into the platform. This allows users to monitor these events across various vehicles or devices conveniently.
- **GPS-independent event processing:** The platform processes and displays driver absence events even if the data packet lacks valid GPS coordinates. These events are recorded regardless of whether they occur inside or outside a designated geofence. The Inside/Outside geofence settings are bypassed in this case, ensuring that no critical event is missed.
