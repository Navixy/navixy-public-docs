---
description: Configure the Social distance block in Navixy so the device reports when another tagged device stays within a set distance for too long.
---

# Social distance

Uses the device's built-in Bluetooth Low Energy (BLE) module to detect when another tracked device comes too close. When two devices stay within the set distance for longer than the set time, an event is reported to the Navixy platform.

{% hint style="info" %}
To receive notifications, create a corresponding alert rule in addition to enabling this block.
{% endhint %}

## Settings

* **Enable**: turn proximity monitoring on or off.
* **Distance**: the minimum allowable distance between devices in meters. Measured in the absence of interference and obstacles. Real-world BLE range is affected by walls, metal, and other obstructions.
* **Duration threshold (seconds)**: how long the two devices must remain within the distance before an event is raised. A short threshold triggers on brief passes. A longer threshold only triggers on sustained proximity.

## Availability

Appears on device models that have a built-in Bluetooth module with proximity detection support.
