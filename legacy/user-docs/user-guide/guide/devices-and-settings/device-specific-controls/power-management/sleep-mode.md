---
description: Configure power-save sleep conditions on a battery GPS device in Navixy with the Sleep mode block. Available fields depend on the device model.
---

# Sleep mode

Controls how a battery-powered device sleeps to save power. A sleeping device reports less often, which extends battery life on assets that aren't continuously powered.

## Settings

The fields depend on the device. You may see:

* **Sleep conditions**: what triggers sleep mode, engine off, no motion, or no communication from the Navixy platform.
* **Sleep and deep-sleep durations**: how long a condition must hold before the device enters light sleep or deep sleep. Light sleep reduces reporting frequency; deep sleep cuts it further to save more power.
* **Wake-up intervals**: how often the device wakes to report while in sleep or deep-sleep mode.
* **Voltage thresholds**: on some models, the battery voltage levels that trigger light or deep sleep.
* **Stay online on external power**: on some models, a toggle to stay in active mode when the device is connected to external power.

## Availability

Appears on battery-powered device models that expose sleep settings.

## See also

* [Tracking mode](../../location-and-movement/tracking-mode.md): power-save options that overlap with sleep mode.
* [Connection state](../../connectivity/connection-state.md): set the offline timeout long enough that a sleeping device isn't shown offline too early.
