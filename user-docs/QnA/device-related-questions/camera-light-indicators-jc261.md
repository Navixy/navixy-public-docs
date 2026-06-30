---
title: Camera light indicators (JC261)
description: "Diagnose JC261 LED status: red indicates power, green shows GPS lock, and blinking blue signals missing cellular connectivity. Verify APN configuration"
---

# Camera light indicators (JC261)

## Question

Why is my JC261 camera's blue LED blinking and it's not sending data?

## Answer

The JC261 camera has three indicator lights on the back panel:

* Red: power
* Green: GPS
* Blue: GSM

When the camera has not yet acquired satellites, the green LED blinks until a proper GPS fix is obtained.

However, the blue LED is the one that most frequently causes issues. When it blinks, it indicates that the device doesn't have cellular network connectivity to transmit data. This is often due to an incorrect APN configuration.

Always make sure that the APN settings were entered correctly.

If the APN is correct, contact the SIM provider to verify the SIM is active.

![](<../.gitbook/assets/Unknown image (83)>)
