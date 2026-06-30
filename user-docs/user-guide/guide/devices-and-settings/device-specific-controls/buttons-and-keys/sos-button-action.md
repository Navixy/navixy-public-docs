---
description: Configure the SOS button action in Navixy so an SOS press sends an alert, or makes a call to a phone number and sends an alert.
---

# SOS button action

Sets what happens when the device's SOS button is pressed.

## Settings

* **Mode**:
  * **Send alert only**: the device sends an SOS event to the Navixy platform. The event appears in the device's history and can trigger platform alert rules (such as a push notification or email to a dispatcher).
  * **Make call to phone number and send alert**: the device both places a voice call to the configured number and sends the SOS event to the platform.
* **Phone number**: the number to call in the second mode. Use international format (for example, `+12025550100`).

## Availability

Appears on device models that have an SOS button.

## See also

* [Call buttons](call-buttons.md): map other device buttons to phone numbers.
* [Pressing SOS button](../../../events-and-notifications/safety/pressing-sos-button.md): the platform rule for SOS events.
