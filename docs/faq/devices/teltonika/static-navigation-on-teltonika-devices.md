---
title: Static navigation on Teltonika devices
description: In static navigation mode, a Teltonika device keeps sending data but stops updating its position. Turn the mode off or change its source in the device configuration
tags:
  - troubleshooting
---

# Static navigation on Teltonika devices

## Question

My Teltonika device stays connected, but its position doesn't change. Why?

## Answer

The device may be in static navigation mode. In this mode, a Teltonika device keeps sending data over the mobile network, but it stops updating its coordinates when it decides that it isn't moving or that the ignition is off. It decides this from its own movement or ignition source settings. In Navixy, the device stays connected while its position on the map stays the same.

The configuration SMS that Navixy sends at activation sets only the APN, the server address, and the reporting intervals. It doesn't change static navigation. To fix this, turn static navigation off, or change its movement or ignition source, in Teltonika Configurator. The settings are described in the Teltonika documentation of your model.
