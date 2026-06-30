---
title: Why does my history show too many stops?
description: Adjust min_speed and min_parking thresholds in Parking Detection to reduce trip fragmentation caused by frequent speed fluctuations
---

# Why does my history show too many stops?

## Question

My device has too many short stops. How do I fix this?

## Answer

* If your route history shows multiple short stops or “gaps,” it usually means the device’s speed often drops below the defined **min\_speed** threshold for longer than the **min\_parking** time. In this scenario, the system interprets each drop in speed as a new parked state, ending one trip and starting another when the speed picks up again.
* Other factors like ignition status or motion sensor data can also influence whether the system flags a stop. For example, if the ignition is off or motion is not detected for the duration of **min\_parking**, the track is considered ended.
* To reduce gaps, review and adjust the **min\_speed** and **min\_parking** settings as needed. You can learn more by visiting the official Parking detection documentation.

## Links

* [Parking detection documentation](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/movement-monitoring/parking-state-detection)
