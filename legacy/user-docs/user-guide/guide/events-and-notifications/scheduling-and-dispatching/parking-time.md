---
description: Get notified when a vehicle remains parked beyond a configured time limit. Bind to geofences to monitor dwell time in specific locations.
---

# Parking time

The Parking Time rule is designed to notify users when a vehicle's parking duration exceeds a specified limit. This rule is crucial for monitoring how long vehicles remain stationary, ensuring that drivers adhere to set time standards for parking and dwell periods.

For instance, if the allowed parking duration is set to 30 minutes, the system alerts users whenever a vehicle remains parked beyond this limit. These notifications can be delivered through the user interface, email, or SMS, and users can also generate reports to review these events historically.

## Rule settings

**Allowed parking time**: The parking time begins counting as soon as the vehicle enters a parked state. Users can configure the allowed parking duration, with options ranging from 5 to 60,000 minutes. The timer resets when the vehicle exits the parked state. For more detailed information on parking detection, see the [Parking detection](https://www.navixy.com/docs/user/guide/devices-and-settings/location-and-movement/parking-detection-block) article.

To monitor dwell time in specific areas, bind the rule to the designated geofences.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- The **Parking time** alert has a 10-second reset timer, meaning the alert doesn't trigger more frequently than once every 10 seconds. If an event occurs while the rule is waiting for the reset, the Navixy platform omits the event, including in reports.
- This rule is processed in the cloud and is not tied to any specific hardware, allowing it to be applied to multiple GPS devices simultaneously. This flexibility enables users to manage several GPS devices within a single rule.
