---
description: 'Get ADAS alerts from video telematics devices: lane departure, forward collision, headway monitoring, pedestrian detection, and traffic sign recognition.'
---

# ADAS warnings

## Overview

Advanced Driver Assistance Systems (ADAS) are engineered to elevate driver safety by delivering real-time alerts and monitoring while on the road. ADAS uses advanced algorithms and machine vision technologies, powered by AI, to analyze data from video cameras and lidar sensors. This enables the system to accurately detect potential hazards and unsafe driving behaviors, ensuring a proactive approach to road safety in line with the latest advancements in vehicle telematics.

The Navixy platform logs and can alert users to the following events:

<table><thead><tr><th width="244.90911865234375">Event</th><th>Description</th></tr></thead><tbody><tr><td><strong>Lane departure warning (LDW)</strong></td><td>Alerts the driver when the vehicle unintentionally drifts out of its lane, helping to prevent potential collisions due to driver inattention.</td></tr><tr><td><strong>Forward collision warning (FCW)</strong></td><td>Warns the driver of an imminent collision with a vehicle or object ahead, allowing for timely braking or evasive action to avoid an accident.</td></tr><tr><td><strong>Headway monitoring warning (HMW)</strong></td><td>Monitors the distance to the vehicle ahead and alerts the driver if the following distance becomes unsafe, promoting safer driving habits.</td></tr><tr><td><strong>Pedestrian in danger zone (PDZ)</strong></td><td>Detects when a pedestrian is in close proximity to the vehicle, particularly in blind spots, to prevent accidents and enhance road safety.</td></tr><tr><td><strong>Pedestrian collision warning (PCW)</strong></td><td>Issues a warning when a potential collision with a pedestrian is detected, giving the driver time to react and prevent an incident.</td></tr><tr><td><strong>Traffic sign recognition (TSR)</strong></td><td>Identifies and alerts the driver to important traffic signs, such as speed limits or stop signs, to ensure compliance with road regulations.</td></tr></tbody></table>

Monitoring these events helps improve driver awareness, reduce the risk of accidents, and ensure adherence to traffic regulations. This, in turn, supports efforts to enhance driver performance, mitigate liability, and prevent costly fines or vehicle repairs.

## Rule settings

### Event selection

As ADAS events are processed on the Video telematics device side rather than in the cloud, no additional settings are required. Select the events you want to monitor to ensure comprehensive driver behavior tracking.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- **No reset timer:** The ADAS alert does not have a reset timer, meaning an alert is triggered every time an ADAS event is detected.
- **Multiple devices:** Users can select multiple GPS devices to receive ADAS notifications. The only requirement is that the chosen GPS devices must support ADAS events, and this feature must be integrated into the platform. This allows for efficient monitoring of driving behaviors across a fleet of vehicles.
- **GPS-independent event alert:** The platform recognizes and displays ADAS events even if the data packet from the GPS device lacks valid GPS coordinates. In these cases, the Inside/Outside geofence settings are ignored, ensuring that critical events are not missed.
