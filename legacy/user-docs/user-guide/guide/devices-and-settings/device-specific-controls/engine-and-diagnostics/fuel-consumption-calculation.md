---
description: Configure the device-side Fuel consumption calculation block in Navixy by setting fuel type, engine volume, and a multiplier for the estimate.
---

# Fuel consumption calculation

Sets the parameters the device uses to compute fuel consumption itself. This is separate from platform-side [fuel sensors](../../vehicle-sensors/measurement-sensors/).

## Settings

* **Fuel type**: gasoline, diesel, or LPG. Affects the built-in fuel rates the device uses to estimate consumption.
* **Engine volume**: the engine displacement in liters (for example, 2.0 for a 2-liter engine).
* **Multiplier**: a correction factor applied to the estimated result. Use 1.0 for no correction. Set it below 1.0 if the device overestimates consumption, or above 1.0 if it underestimates.

## Availability

Appears on device models that compute fuel consumption on the device side.

## See also

* [Fuel level sensor](../../vehicle-sensors/measurement-sensors/fuel-level-sensor.md): platform-side fuel monitoring with calibration.
