---
description: Receive alerts when a GPS device's primary or backup battery charge drops below the device's threshold, helping you prevent tracking downtime.
---

# Battery low (Backup battery low)

A GPS device reports a battery low alert when the charge level of its internal battery drops below a predetermined threshold. This alert is triggered to notify users that the device's power is running low, indicating that the battery needs to be recharged or replaced soon.

In the case of portable GPS devices, this alert signals that the primary power source is depleting, while for vehicle-mounted GPS devices, it may indicate that the backup battery is running low, potentially compromising the device's ability to continue tracking if the main power supply is lost.

<details>

<summary>Role of internal batteries in GPS devices</summary>

An internal battery in a GPS device is a built-in power source that plays a crucial role in the device's functionality. Depending on the purpose of the GPS device, this internal battery can serve different roles.

- In portable GPS devices, the internal battery is typically the primary power source. These batteries are essential for tracking assets, people, or vehicles in scenarios where external power may not be available.
- For vehicle-mounted GPS devices, the internal battery usually acts as a backup power source. This backup battery kicks in when the main power supply from the vehicle is interrupted, whether due to disconnection, tampering, or vehicle battery failure. The backup battery ensures continuous tracking and data transmission, providing an extra layer of security and reliability, especially in critical fleet management or anti-theft applications.

</details>

## Two battery monitoring rules

In Navixy, there are two distinct rules designed to monitor the battery status of your GPS devices: the **Low battery** rule and the **Backup battery Low** rule.

- The **Low battery rule** applies to the primary power source of the device, typically used in portable GPS devices or devices solely reliant on internal batteries.
- The **Backup battery low rule** specifically monitors the charge level of secondary or backup batteries, which are commonly found in vehicle-mounted GPS devices.

These are hardware-based rules, meaning the device itself or its configuration determines the trigger threshold for the low battery alert. When the battery charge drops below this threshold, the device reports it, and the Navixy platform sends a notification, allowing users to take proactive steps to recharge the device and prevent downtime.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and Alerts](../README.md).

## Platform specifics

The **Low battery** and **Backup battery low** alerts share several operational similarities, but there is a difference in their reset timers.

- **Reset timers**: The **Backup battery low** alert has a 1-minute reset timer, meaning the alert doesn't trigger more frequently than once per minute. In contrast, the **Low battery** alert has a longer 30-minute reset timer, so it doesn't trigger more frequently than once every 30 minutes. If an event occurs during the reset period for either rule, it is omitted from the Navixy platform, including reports.
- **GPS device selection**: Both rules allow users to monitor multiple GPS devices, provided the GPS devices support the respective events and the feature is integrated on the platform. This capability enables efficient monitoring of battery levels across various vehicles or devices.
- **Event registration**: For both rules, the platform registers and displays the event even if it is received in a packet without valid coordinates. This ensures that users are informed of the event regardless of its location, maintaining consistent oversight of their assets.
