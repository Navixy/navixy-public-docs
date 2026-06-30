---
description: Configure impact force thresholds on the device so it detects and reports collisions to Navixy. Some models support separate weak and strong impact levels.
---

# Accelerometer

Reports an impact when the device detects force above a configured threshold for a configured duration.

## Settings

Some models expose a single threshold; others provide separate **Weak impact** and **Strong impact** thresholds.

* **Force** (weak / strong): the minimum impact force required to register an event. On models with two levels, the weak threshold triggers a low-priority event and the strong threshold triggers a high-priority event.
* **Duration**: how long the force must be sustained to register an impact. Single bumps shorter than this value are ignored.

{% hint style="info" %}
Both values are minimums for detection. A lower value makes the sensor more sensitive.
{% endhint %}

## Availability

Appears on device models with a configurable impact accelerometer.
