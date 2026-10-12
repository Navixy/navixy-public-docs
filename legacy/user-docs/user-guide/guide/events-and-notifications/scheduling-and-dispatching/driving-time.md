---
description: Alert when a driver exceeds the allowed continuous driving time. Set the maximum driving duration and the minimum parking time required to reset the timer.
---

# Driving time

The **Driving time** rule is designed to monitor and enforce safe driving practices by tracking the work and rest periods of drivers. This rule ensures that drivers comply with driving time regulations, helping to prevent fatigue-related incidents and promote overall road safety.

When a driver exceeds the allowed continuous driving time, the system generates a notification to alert the user. The rule automatically resets once the driver has rested for a specified period, which you can configure according to your needs.

## Rule settings

### Allowed driving time

This setting defines the maximum duration a driver can continuously drive before a notification is triggered. Once this limit is reached, the system alerts you to take necessary action.

### Minimum parking time for reset

This setting specifies the minimum parking duration required to reset the driving time rule. The reset timer begins only when the device detects that the vehicle has entered a parking state.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- The **Driving time** rule operates by tracking the total time a driver spends driving continuously. Once the maximum allowed driving time is exceeded, the system generates an alert.
- The rule resets after the vehicle has been parked for the minimum duration specified in the settings, allowing the driver to begin a new driving period.
- This rule is processed in the cloud, enabling it to be applied across multiple GPS devices simultaneously. This flexibility allows you to monitor several drivers within a single rule setup.
- The system helps ensure compliance with driving regulations, reduce driver fatigue, and promote safer driving practices, contributing to overall fleet safety and efficiency.
