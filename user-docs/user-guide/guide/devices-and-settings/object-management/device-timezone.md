---
description: Correct the timezone offset for GPS devices that report timestamps in local time. Prevents tracking data misalignment when the device clock doesn't match UTC.
---

# Device timezone

The **Device timezone** block allows you to adjust the timezone settings for specific device models. A GPS device's timezone offset determines how the timestamps in the messages reported to the Navixy platform are interpreted. If the timezone offset is incorrect, it can lead to inaccurate tracking data, as the timestamps don't align correctly with the actual time of events.

{% hint style="danger" %}
Navixy strongly recommends keeping the device set to UTC+0h as the standard timezone setting to ensure consistency and accuracy in tracking data. However, if your device is configured with a different timezone, this block allows you to make the necessary adjustments.
{% endhint %}

## When to use

Only change the timezone if you are certain that the current setting is incorrect. Misalignment of the timezone can lead to errors in the displayed data, making it difficult to monitor the device accurately on the platform.

## How to adjust the device timezone

1. **Access the block:** Navigate to the **Devices and settings** section and select the device you wish to adjust.
2. **Change the timezone:** Use the **Device timezone** block to select the appropriate timezone based on the device's current configuration.
3. **Save changes:** After making the adjustment, save the settings to apply the new timezone.

## Availability

Appears on specific device models that support a configurable time-zone offset.
