---
description: Set up the odometer data source, match it to the vehicle's actual reading, and apply a correction multiplier to keep mileage totals accurate.
---

# Odometer

Tracks a vehicle's total mileage and lets you correct measurement error. Readings can come from GPS-based mileage, hardware/CAN mileage, a tachograph, or a measurement sensor you've created. It integrates with [Fleet maintenance](../../fleet-management/maintenance.md) so you can schedule service by mileage and receive reminders.

![](../../../.gitbook/assets/image-20240815-181307.png)

## Settings

* **Data source**: GPS-based mileage, hardware/CAN mileage, tachograph, or a measurement sensor. Additional sources appear only after you create the matching [measurement sensor](../vehicle-sensors/measurement-sensors/).
* **Current value**: Set the odometer to match the vehicle. Kilometers, 0–99,999,999, up to 2 decimals.
* **Correction multiplier**: Adjust readings up or down by a percentage. Range about −95% … +95% (a positive value increases readings, negative decreases them).

## Activation

{% stepper %}
{% step %}
### Open the block

Open **Devices and settings**, select the object, and go to the **Odometer** block.
{% endstep %}

{% step %}
### Add the odometer

If none exists yet, click **Add odometer**.
{% endstep %}

{% step %}
### Choose a source and set the value

Pick the data source, set the initial mileage value, and click **Save**.
{% endstep %}
{% endstepper %}

## Availability

Appears on device models that support an odometer.

## Limitations

* The Navixy platform odometer rarely equals the vehicle's physical odometer. It counts all GPS points and allows for manufacturer error. Use the multiplier to correct a consistent offset, and collect about 200 km of data first to estimate it.
* Additional sources (e.g. CAN mileage) only appear after the matching measurement sensor is created.
* The **Odometer** value is also accessible from the device's [Object widget](../../tracking/objects-list/object-widget.md).

## See also

* [Engine hours](engine-hours.md): the equivalent counter for engine running time.
* [Fleet maintenance](../../fleet-management/maintenance.md): schedule service by mileage.
