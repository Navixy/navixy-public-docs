---
description: Activate engine hours by choosing an ignition or CAN-bus data source and setting an initial value, then use the counter to trigger maintenance reminders.
---

# Engine hours

Engine hours is a tool that allows owners of vehicles and special machinery to monitor engine running time and schedule [maintenance works](../../fleet-management/maintenance.md) based on this data.

To activate Engine hours:

1. Open [Devices and settings](../), choose the object you need, and proceed to the **Engine hours** block.
2. Click the **Add engine hours** button.
3. Choose a data source: ignition or engine hours from the CAN bus.
4. Set the initial value for engine hours (0 to 99,999,999 hours, up to 2 decimal places).
5. Click **Save**.

{% hint style="warning" %}
To receive data from the ignition sensor, create it [beforehand](../vehicle-sensors/discrete-sensors/ignition-source.md) as a discrete sensor.
{% endhint %}

{% hint style="info" %}
Unlike the [Odometer](odometer.md), engine hours has **no correction multiplier**.
{% endhint %}

To display engine hours data in the **Tracking** module, add the **Engine hours widget** to the widget bar at the bottom of the screen. Just click the **+** button at the upper-right corner of the widget bar and add the **Engine hours widget**.

## Availability

Appears on device models that report ignition or CAN-bus engine hours data.

## See also

* [Odometer](odometer.md): the equivalent counter for distance traveled, with correction multiplier support.
* [Fleet maintenance](../../fleet-management/maintenance.md): schedule service intervals by engine hours.
* [Ignition source sensor](../vehicle-sensors/discrete-sensors/ignition-source.md): the discrete sensor required when using ignition as the data source.
