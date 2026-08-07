---
description: >-
  Control the offline timeout for each device so power-saving and low-frequency
  trackers stay accurately shown as connected in Navixy.
---

# Connection state

Sets how long a device may be silent before it's marked offline, so infrequently-reporting or power-saving devices aren't shown as disconnected too early. It affects only the offline/disconnected status indicator, not how the device reports (that's [Tracking mode](../location-and-movement/tracking-mode.md)).

{% hint style="info" %}
You can monitor the [Connection state](../../tracking/objects-list/connection-state.md) of your devices in the [Objects list](../../tracking/objects-list/) in the Tracking module and the **X-GPS Monitor** mobile app. It's shown as a color-coded circle in each device's widget.
{% endhint %}

![](../../../.gitbook/assets/image-20240815-034950.png)

## Settings

* **Time interval**: The duration without data after which the device is considered disconnected. Choose minutes, hours, or days. You can set it from 1 minute up to about 3,000 days.

{% hint style="warning" %}
This field shows **10 minutes** until you save a different value. That number is only a placeholder, not a real default. If you leave the field untouched and never save a value, the Navixy platform falls back to a built-in timeout instead. This built-in timeout varies by device and connection type, and it can be shorter or longer than 10 minutes. The **Reset to defaults** button doesn't clear this setting. The button only refills the field with 10 minutes, and saving then stores 10 minutes as an explicit value for this device. Set an explicit time interval here if you need predictable, consistent **Offline** timing for this device.
{% endhint %}

## Availability

Appears on applicable devices for users with edit rights.

## Limitations

* This affects only the offline status indicator, not the device's reporting behavior.
* For devices in deep sleep or power-save, set the timeout long enough that a sleeping device isn't shown offline prematurely. Coordinate with [Tracking mode](../location-and-movement/tracking-mode.md) and [Sleep mode](../device-specific-controls/power-management/sleep-mode.md).

## See also

* [Connection state](../../tracking/objects-list/connection-state.md): the connection statuses and their color-coded meanings.
