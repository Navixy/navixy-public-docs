---
description: Monitor proximity between GPS-enabled devices and BLE tags using Bluetooth. Useful for detecting dangerous equipment proximity and enforcing safety distances.
---

# Proximity monitoring

The **Proximity monitoring** rule leverages Bluetooth functionality to provide real-time alerts when proximity events occur. This rule is designed to detect precise distances between objects equipped with GPS devices that have BLE (Bluetooth Low Energy) capabilities, as well as between GPS devices and BLE tags. It is a versatile tool that offers numerous applications across various industries.

This rule is particularly beneficial in environments where maintaining specific distances is critical. Examples include:

- **Dangerous equipment use:** Track the proximity of operators to hazardous equipment, preventing accidents and ensuring compliance with safety regulations.
- **Social distancing in epidemic environments:** Maintain and enforce social distancing measures in workplaces or public areas during epidemics, helping to reduce the spread of contagious diseases.

By employing this rule, organizations can enhance safety, ensure compliance with regulations, and optimize the management of proximity-related activities.

## Rule settings

The functionality of this rule depends on the device's capabilities and configuration, so no specific rule settings are required.

For common settings, see [Rules and alerts](../README.md).

## Navixy platform specifics

- **Reset timer:** The **Proximity monitoring** alert has a 10-second reset timer, meaning the alert event doesn't occur more often than once every 10 seconds. If this type of event occurs in time the rule has been waiting for the reset, this event is omitted by the platform, including the reports.
- **Multiple devices:** This rule allows users to select multiple devices from which they wish to receive notifications. The selected GPS devices must support the **Close proximity** event, and this feature must be integrated on the platform for those GPS devices. This enables users to monitor events across various devices conveniently.
- **GPS-independent event alert:** Whenever the platform identifies a hardware event of this type from a packet of GPS device data with no valid coordinates in it, the platform counts the event as a valid one and displays it regardless of whether the event occurred within or outside the bound geofences to ensure critical notifications.
