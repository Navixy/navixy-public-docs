---
description: Alert in Navixy when a vehicle on an active route stays stationary longer than the configured no-movement timeout. Optionally set a pre-alarm for early notice
---

# No movement

Reports when a device that is expected to be moving stays stationary longer than a set threshold. Useful for monitoring vehicles on active routes, for example, detecting a delivery vehicle that has stopped longer than expected.

{% hint style="info" %}
Unlike [Unauthorized movement](unauthorized-movement.md), which watches a **parked** vehicle for unexpected movement, No movement watches a vehicle expected to be **moving** for unexpected stillness.
{% endhint %}

## Settings

* **Enable**: turn no-movement detection on or off.
* **Timeout**: how long the device may remain stationary before an event is sent to the Navixy platform. Set this to match the longest acceptable stop for the vehicle's task, for example, 30 minutes for a delivery route.
* **Pre-alarm duration**: an optional warning period that fires before the main no-movement event. Use it to trigger an early notification before the full alert, giving the driver time to respond.

## Availability

Appears on device models that support no-movement detection.

## See also

* [Unauthorized movement](unauthorized-movement.md): detect a parked vehicle leaving its position.
* [Tow detection](../../location-and-movement/tow-detection.md): detect towing of a stationary vehicle.
