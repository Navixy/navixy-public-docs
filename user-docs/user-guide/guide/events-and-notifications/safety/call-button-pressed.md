---
description: Get notified when the call button on a GPS device is pressed. For devices equipped with a built-in phone call or radio function to enable rapid communication.
---

# Call button pressed

The **Call button pressed** rule is designed for GPS devices equipped with a built-in call function, typically found in devices capable of voice calls or radio communication.

This rule triggers notifications whenever the device's phone call function is activated, providing a quick way to initiate real-time communication. This feature enhances safety, security, and operational efficiency by ensuring rapid responses in critical situations and contributing to smoother operations.

## Rule settings

The functionality of this rule depends on the device's capabilities and configuration, so no specific rule settings are required.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- **Reset timer:** The **Call button pressed** alert has a 1-minute reset timer, meaning the alert doesn't trigger more than once per minute. If an event occurs while the rule is waiting for the reset, the platform omits the event, including in reports.
- **Multiple devices:** This rule allows users to select multiple GPS devices from which they wish to receive notifications. The selected GPS devices must support the **Call button pressed** event, and this feature must be integrated on the platform for those GPS devices. This enables users to monitor events across various devices conveniently.
- **GPS-independent event alert:** The platform recognizes and counts this type of hardware event even if the data packet lacks valid GPS coordinates. The event is displayed regardless of whether it occurred within or outside the defined geofences. The Inside/Outside geofence settings are ignored in this case, ensuring that no critical event is omitted.
