---
description: Use on-board voltage or motion as the ignition source when the GPS device has no ignition wire. Set the voltage threshold or motion timing in Navixy.
---

# Virtual ignition

Lets the device derive ignition state from on-board voltage or motion when the ignition wire is not connected.

## Settings

* **Mode**: derive ignition from board voltage or from the motion sensor.
* **Ignition-on voltage**: the voltage above which ignition counts as on, in voltage mode.
* **Rest duration to off**: how long without motion before ignition counts as off, in motion mode.
* **Motion duration to on**: how long motion must last before ignition counts as on, in motion mode.

## Availability

Appears on device models that support virtual ignition.

## See also

* [Ignition source](../../location-and-movement/ignition-source.md): the main block for choosing the ignition detection mode.
