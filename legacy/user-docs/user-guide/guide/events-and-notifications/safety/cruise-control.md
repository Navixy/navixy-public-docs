---
description: Get notified when Cruise control is switched on or off on a vehicle. Monitor its impact on fuel efficiency, battery management, and driver behavior patterns.
---

# Cruise control

## Overview

Receiving notifications when **Cruise control** is enabled or disabled in a vehicle provides valuable insights for monitoring and managing vehicle performance. Here are some key benefits:

* **Fuel efficiency monitoring:** **Cruise control** helps maintain a consistent speed, which can improve fuel efficiency. Notifications allow users to track **Cruise control** usage and assess its impact on fuel consumption.
* **Battery management:** For electric or hybrid vehicles, **Cruise control** usage can affect battery life and range. Notifications help users monitor its impact on battery usage, enabling better management of the vehicle's electric power.
* **Driving behavior analysis:** Tracking when and where **Cruise control** is used offers insights into driver behavior. This data can be used to analyze driving habits and identify opportunities for improvement, such as reducing excessive or increasing underutilized **Cruise control** usage.
* **Performance evaluation:** Monitoring **Cruise control** usage can aid in evaluating vehicle performance. Users can compare **Cruise control** with other driving modes to assess efficiency across different routes and driving conditions.
* **Safety and comfort:** **Cruise control** enhances comfort and safety during long journeys. Notifications ensure users are aware of whether Cruise control is active, allowing them to adjust their driving style as needed.

These benefits may vary depending on the vehicle's make, model, and additional features.

## Rule settings

This rule doesn't require any specific settings.

For common settings, see [Rules and alerts](../README.md).

## System operation details

* **Reset timer:** The **Cruise control wwitched on/off** alert has a 10-minute reset timer, preventing the alert from triggering more than once every 10 minutes. If another event occurs within this reset period, it is omitted from the platform and reports.
* **Multiple devices:** Users can select multiple GPS devices to receive notifications. The selected GPS devices must support **Cruise control** Switched ON/OFF events and have this feature integrated into the platform. This allows monitoring of Cruise control events across various vehicles.
* **GPS-independent event alert:** The platform registers and displays **Cruise control** events even if the data packet lacks valid GPS coordinates. The Inside/Outside geofence settings are ignored for this rule, ensuring that critical events are not missed.
