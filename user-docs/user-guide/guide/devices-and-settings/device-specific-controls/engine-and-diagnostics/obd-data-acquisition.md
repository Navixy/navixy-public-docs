---
description: Configure the OBD data acquisition block in Navixy to enable PID reports on an OBD device and set the collection interval and upload batch size.
---

# OBD data acquisition

Controls how an OBD device collects and uploads PID (Parameter ID) diagnostic data from the vehicle's OBD-II port. PID data includes standard engine diagnostics such as RPM, vehicle speed, coolant temperature, and fuel level, parameters not transmitted over GPS, but available through the vehicle's CAN bus.

## Settings

* **Enable PID reports**: turn OBD data collection on or off.
* **PID data collection interval**: how often the device samples a new PID reading (for example, every 10 seconds). Shorter intervals give more granular data but increase data consumption.
* **Upload records count**: how many records the device accumulates before sending a batch to the Navixy platform. For example, a value of 10 with a 10-second interval means data uploads roughly every 100 seconds.

## Availability

Appears on OBD device models that support PID data acquisition.

## See also

* [Engine state detection](engine-state-detection.md): configure voltage-based engine on/off detection on the same device family.
