---
description: >-
  How GPS and IoT devices of different brands and protocols connect to Navixy
  through the Navixy Generic Protocol, and what the platform reads from them.
tags:
  - guide
noIndex: true
---

# How to integrate your devices

This page explains how devices reach the Navixy platform: the protocol they speak to it, the kinds of hardware and data sources it takes in, and what it reads from a device once connected. It is for fleet operators, system integrators and hardware manufacturers choosing or connecting hardware.

Navixy connects GPS and IoT devices regardless of brand, model, or protocol. Whether you're using a Teltonika in South America, a Queclink in Asia, or a custom-built sensor network, the Navixy platform brings them into one system. Its device-agnostic architecture lets you focus on outcomes rather than compatibility.

## One universal language: the Navixy Generic Protocol (NGP)

Integration rests on NGP, the [Navixy Generic Protocol](https://navixy.com/en/devices/navixy/navixy-generic-protocol/), an open, universal language for telematics data.

* Works with modern GPS and IoT hardware
* Standardizes incoming and outgoing data
* Freely available for manufacturers to adopt
* Simplifies compatibility and device onboarding

If your device sends data, it most likely already integrates with Navixy. Check the [supported devices](https://www.navixy.com/devices/) list for its model or manufacturer.

If it doesn't yet, the Navixy platform can translate a proprietary protocol into NGP, so existing hardware keeps working while the data pipeline moves to the current platform.

## Connect everything: from OEM telematics to IoT sensors

Navixy integrates OEM modules, aftermarket devices, and IoT sensors in one interface.

* OEM telematics: Connect data streams from systems like GM OnStar, Ford Pro, or Volvo Connected Vehicle, and see diagnostics directly on your dashboard.
* IoT sensors: Integrate wireless temperature tags, fuel sensors, or Bluetooth beacons for logistics and asset tracking.
* Satellite and LPWAN: Bring in remote assets through satellite, LoRaWAN, or private networks.

Store-and-forward logic protects data in low-connectivity environments: a device buffers its messages while offline and sends them when the connection returns.

## Fast and deep integration

Adding a new device shouldn't take weeks. With Navixy, it often takes days.

### What makes it fast

* Automated provisioning and SIM configuration
* Remote management: Update firmware, change settings, or activate devices over the air
* Health monitoring tools for troubleshooting

### What makes it deep

Navixy doesn't only show position and speed. It reads the features your hardware provides:

* Fuel level and theft alerts
* Driver ID and access control
* Tire pressure, temperature, and other telemetry

## No vendor lock-in

Navixy is designed to avoid lock-in. You can mix devices from any manufacturer, and change them at any time, without re-architecting your software.

* Choose the best hardware for each region or task
* Avoid OEM fees or data restrictions
* Keep ownership and control of your telemetry data

This flexibility lets a business scale and experiment, knowing the platform adapts rather than constrains.

## Scalable and future-ready

Navixy is built for scale. Its cloud-based architecture handles a high volume of daily events from diverse devices while staying fast, reliable, and globally accessible.

Because the platform is protocol-extensible, it is ready for new device classes, from EV telemetry to AI-powered sensors.

## Join the Navixy ecosystem

Navixy works with device manufacturers and IoT innovators worldwide. Open APIs, SDKs, and developer documentation make it possible to build integrations or bring new devices to market faster.

Whether you're a fleet operator, a system integrator, or a hardware manufacturer, Navixy provides the foundation to connect and scale.

## See also

* [Supported devices](https://www.navixy.com/devices/): the models and manufacturers Navixy integrates today.
* [Contact Navixy](https://navixy.com/en/contact) to start an integration for a device that isn't listed.
