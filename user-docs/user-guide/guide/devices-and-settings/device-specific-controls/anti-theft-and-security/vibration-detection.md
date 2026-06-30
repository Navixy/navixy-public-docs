---
description: Set sensitivity and timing so the device detects tampering or vibration after the ignition switches off, then reports an event to Navixy
---

# Vibration detection

Uses the device's motion sensor to detect vibration or tampering after the ignition is off and report an event to the platform.

## Settings

* **Enable**: turn vibration detection on or off.
* **Sensitivity level**: how easily the motion sensor triggers. **High** responds to lighter vibration; **Low** requires stronger or longer vibration. Some models also offer a **Very low** level.
* **Activation after ignition (seconds)**: the delay after the ignition switches off before detection arms. This prevents false alarms during normal parking maneuvers.
* **Vibration duration (seconds)**: how long vibration must continuously last to count as an event. Single bumps shorter than this value are ignored.
* **Pre-alarm duration (seconds)**: a window around each ignition event during which vibration is ignored. Prevents false alarms when the engine is started or stopped.

## Availability

Appears on device models that support vibration detection.

## See also

* [Vibration settings](vibration-settings.md): adjust the motion sensor sensitivity on devices that expose it separately.
