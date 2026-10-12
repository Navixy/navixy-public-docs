---
description: Get notified when a vehicle deviates from a planned route. Uses route-type geofences to define the permitted path and alerts when the object moves outside it.
---

# Deviation from the route

The route deviation rule enables monitoring of deviations from a predefined route. This rule ensures that vehicles or objects adhere to planned routes, improving efficiency and enhancing security by promptly identifying and addressing any deviations. Users receive notifications whenever a vehicle or object deviates from the specified route, helping to maintain compliance and optimize operations.

## Rule settings

### Route geofences around paths

To use the route deviation rule, you must first create one or more route geofences in the Navixy platform. Follow these steps to set up the rule:

1. **Create route Geofences**: Before setting up the rule, ensure you have created route geofences categorized as **Route**.
2. **Select Geofences**: Choose the route geofences you want to monitor for deviations. Only geofences categorized as **Route** can be used for this purpose.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- The **Deviation from the route** alert has a 10-second reset timer, meaning the alert event doesn't occur more often than once every 10 seconds. If an event occurs while the rule is waiting for the reset, the platform omits the event, including in reports.
- This rule is processed in the cloud and not tied to any specific hardware, allowing it to be applied to multiple GPS devices simultaneously. This flexibility enables you to select several GPS devices within a single rule.
