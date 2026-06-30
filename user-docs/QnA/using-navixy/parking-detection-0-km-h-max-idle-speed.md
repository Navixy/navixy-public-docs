---
title: Parking detection 0 km/h max idle speed
description: Set maximum idle speed above 0 km/h in Parking Detection to classify vehicles as parked; a value of 0 disables parking detection entirely
---

# Parking detection 0 km/h max idle speed

## Question

What happens if the max idle speed is set to 0 km/h in the Parking Detection configuration?

## Answer

If the idling speed is set to 0 km/h, the platform's logic requires receiving a speed lower than 0 km/h in order to classify the status as parked. This is impossible because devices never send negative speeds.

As a result, setting the threshold to 0 km/h negatively affects route processing:

* The platform can't separate trips by parking events.
* The unit always appears as "moving" (movement arrow), even when it is actually parked.

Therefore, setting the idle speed threshold to 0 km/h is not recommended.

For this reason, we recommend:

* Checking the motion sensitivity settings on the device.
* Possibly escalating the issue to the manufacturer, so they can help improve movement-detection accuracy.
