---
description: Guard a parked vehicle by arming the device's motion sensor and perimeter detection. Set time windows, sensitivity thresholds, and a parking radius in Navixy.
---

# Security mode

Arms the device's sensor and perimeter detection to report tampering or displacement of a guarded vehicle.

## Settings

The fields depend on the device. You may see:

* **Sensor mode**: when the motion sensor monitors the vehicle.
  * **Disabled**: sensor monitoring off.
  * **Constantly enabled**: active at all times when armed.
  * **Enabled in one period / two periods**: active only during configured time windows, such as overnight or outside business hours.
* **Perimeter mode**: how the device responds to the vehicle leaving its parked position.
  * **Disabled**: perimeter monitoring off.
  * **One-time alert with disabling**: triggers one event, then disarms automatically.
  * **Constantly enabled**: triggers an event on each detected exit and stays armed.
  * **Security breach with new point storage**: triggers an event and updates the reference position to the new location, so continued movement generates further events.
* **Period settings**: start and end times for the scheduled sensor-mode windows.
* **Amplitude and duration**: how strong and how long a movement signal must be before it registers. Higher values reduce false alarms from passing traffic or minor bumps.
* **Ignore time upon activation**: a delay after arming before motion detection starts, preventing false alarms during placement or parking.
* **False activation protection**: a double-check step that confirms motion before raising an alarm, reducing false alerts from brief or incidental signals.
* **Perimeter diameter**: the radius of the parking zone in meters. Movement beyond this boundary triggers the perimeter alarm.

## Availability

Appears on device models that support a guard or security mode.
