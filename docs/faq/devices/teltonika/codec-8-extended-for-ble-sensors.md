---
title: Codec 8 Extended for BLE sensors on Teltonika devices
description: The readings of Teltonika EYE sensors and the data of BLE tags use element numbers above 255, which only Codec 8 Extended carries
tags:
  - reference
---

# Codec 8 Extended for BLE sensors on Teltonika devices

## Question

Why doesn't Navixy receive the readings of a BLE sensor from my Teltonika device?

## Answer

The device may be sending data in Codec 8. Teltonika devices send data in one of two formats:

* **Codec 8** carries data elements numbered up to 255.
* **Codec 8 Extended** carries data elements numbered up to 65,535.

The readings of Teltonika EYE sensors, and the name, MAC address, and signal strength of a BLE tag, use element numbers above 255. Navixy receives them only when the device sends Codec 8 Extended. BLE temperature and humidity in slots 1 to 4 of an FMB device use element numbers below 256, so they also arrive in Codec 8.

The configuration SMS that Navixy sends at activation doesn't change the codec. Set Codec 8 Extended on the device, for example in Teltonika Configurator.
