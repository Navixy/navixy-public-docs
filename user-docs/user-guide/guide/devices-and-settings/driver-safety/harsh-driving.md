---
description: Set harsh-driving thresholds on the GPS device in Navixy. Events generated here power Notifications and Eco-driving reports. Units vary by device family.
---

# Harsh driving

Configures device-side detection of harsh acceleration, braking, and cornering. When the device's accelerometer exceeds the configured thresholds, it generates a **Harsh driving** event, which feeds [Notifications](../../events-and-notifications/safety/harsh-driving.md) and [Eco-driving reports](../../fleet-management/eco-driving.md).

![](../../../.gitbook/assets/image-20240815-214000.png)

## Settings

**Enable or disable**, plus thresholds for acceleration, braking, and (usually) cornering. Different devices measure harsh driving differently, by g-force, by speed change, or by force percentage. Your device manual states which method applies. The unit and tuning style differ by device family:

* **G-force thresholds** (g-force is the force felt during acceleration or braking relative to gravity; e.g. about 0.04–3 g, lower = more sensitive), e.g. BCE.
* **Speed-change thresholds**: A km/h change over a detection time, sometimes with a cornering angle (0–180°) and cornering speed, e.g. GoSafe, Concox, Ruptela.
* **Speed-range tables** (different thresholds at low, medium, or high speed) and **vehicle presets** (car, bus, or truck), e.g. some Teltonika/Queclink.
* **Force percentages** (e.g. 25–100%), e.g. Tramigo.
* Some models can additionally use the **motion sensor** alongside GPS.

{% hint style="info" %}
Because units and fields are vendor-specific, the exact controls depend on your device. Tune thresholds to the vehicle type. A sedan accelerates faster than a bus, so the appropriate threshold values differ.
{% endhint %}

## Availability

Appears on devices that support harsh-event detection (not all models do, and some report only a subset of event types).

## Limitations

* Thresholds live here, not in the Eco-driving report, the report only assigns penalties to events the device already generated.
* Events while the vehicle is essentially stopped are ignored.

## See also

* [Harsh driving notifications](../../events-and-notifications/safety/harsh-driving.md): alert on harsh-driving events.
* [Eco-driving reports](../../fleet-management/eco-driving.md): analyze driving behavior.
