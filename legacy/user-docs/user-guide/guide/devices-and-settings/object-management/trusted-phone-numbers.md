---
description: Control which phone numbers may send SMS and USSD commands to a GPS device, protecting it from unauthorized configuration. Up to five numbers.
---

# Trusted phone numbers

The numbers allowed to send **SMS/USSD commands** to the device. Restricting command sources protects the device from unauthorized configuration or control. This setting is part of the device's general object settings.

<!-- SCREENSHOT: Object settings, Trusted phone numbers field (multi-entry). Annotate: add/remove number control, the 5-number limit. -->

## Settings

* **Trusted numbers**: A list of phone numbers permitted to send commands to the device. Add up to 5 numbers. Each entry is validated as a phone number.

## Availability

Available for devices on accounts where the trusted-numbers feature is enabled, and only for non-blocked devices.

## Limitations

* The limit is 5 numbers per device.
* Trusted numbers can be copied to many devices at once, along with label, group, and tags.
* This controls who may command the device by SMS/USSD: It doesn't change how the device reports (see [Tracking mode](../location-and-movement/tracking-mode.md)) or its SIM settings (see [Phone number](phone-number.md)).

## See also

* [Phone number](phone-number.md): the device's SIM and APN settings.
* [Object label](object-label.md): label, group, and tags.
