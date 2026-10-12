---
description: How BLE and other wireless sensors send readings to a GPS device, how they compare with wired sensors, and how Navixy handles BLE tag readings.
tags:
  - fundamentals
---

# Wireless telematics sensors

A wireless telematics sensor measures a value, such as temperature or door state, and sends it to a GPS device over radio instead of a cable. This article explains how these sensors work, which values they commonly measure, and when a wireless sensor is a better choice than a wired sensor. It is for fleet managers and installers who plan to add sensors to a fleet.

## Bluetooth Low Energy in telematics sensors

Many wireless telematics sensors use Bluetooth Low Energy (BLE). BLE is a short-range radio standard designed for very low power use. The Bluetooth Special Interest Group (Bluetooth SIG) added it to the Bluetooth Core Specification in 2010. BLE operates in the 2.4 GHz band, which is available worldwide without a license.

A BLE sensor is a small, battery-powered unit. It's mounted where the value is measured: inside a refrigerated compartment, on a trailer door, or on a piece of equipment. The GPS device in the vehicle receives the readings and sends them to the server together with its own data.

## How a GPS device receives BLE sensor readings

A GPS device receives BLE sensor readings in one of two ways:

* **Broadcast**: The sensor sends short advertising packets at a fixed interval. An advertising packet is a message that any nearby BLE receiver can read without a connection. The GPS device scans for these packets and reads the values in them. Many telematics sensors work this way, because one GPS device can read many sensors at once.
* **Connection**: The GPS device connects to the sensor and requests the values. BLE organizes these values with the Generic Attribute Profile (GATT). GATT groups the data that a sensor offers into services, and each service holds one or more values called characteristics.

In both cases, the GPS device must support BLE. It also must be configured to accept readings from the sensors that you install. Follow the manufacturer's documentation for this setup.

## Common types of wireless sensors

Wireless sensors in fleet telematics commonly measure the following values:

* Temperature and humidity, for example in refrigerated transport
* Door or hatch state, detected by a magnet sensor
* Movement and tilt, detected by an accelerometer
* Light level, for example to detect that a container was opened
* Fuel level in a tank
* Tire pressure and temperature

Many BLE sensors also report the state of their own battery. This value helps you replace the battery before the sensor stops sending readings.

## Battery life and range

A BLE sensor runs on a small internal battery, often a coin cell. Battery life is usually measured in years. The exact figure depends on how often the sensor transmits and at what power. A longer interval between transmissions gives a longer battery life.

Range depends on transmit power, the antenna, and obstacles between the sensor and the GPS device. In open space, a BLE sensor can reach tens of meters. Metal body panels, cargo, and walls shorten this distance. Place the GPS device and the sensors so that the radio path between them is as short and clear as possible.

## Tire pressure sensors on UHF radio

Many tire pressure monitoring systems (TPMS) use UHF radio instead of BLE. A direct TPMS has a pressure sensor inside each tire. Each sensor sends its readings to a receiver in the vehicle. Vehicles in North America usually use 315 MHz, and vehicles in Europe usually use 433 MHz. A receiver for one frequency can't read sensors that transmit on the other frequency.

An indirect TPMS has no pressure sensors. It estimates the pressure from wheel speed data, because an underinflated tire turns at a different speed.

## Wireless and wired sensors compared

The following table compares wireless and wired sensors:

| Aspect | Wireless sensor | Wired sensor |
| --- | --- | --- |
| Installation | No cable to route, so mounting is quick | Needs a cable from the sensor to the GPS device |
| Moving the sensor | Easy to move to another vehicle or trailer | Needs new wiring |
| Power | Internal battery that needs replacement | Power from the vehicle |
| Signal | Can weaken through metal and cargo, or meet interference in the 2.4 GHz band | A cable connection isn't affected by radio obstacles |

Wireless sensors suit trailers, containers, and cargo areas, where a cable is hard to route or the sensor moves between vehicles. Wired sensors suit permanent installations where the sensor must report without interruption.

## How Navixy handles BLE tag readings

The Navixy platform decodes the readings of BLE sensors, also called BLE tags, that a GPS device forwards, for example a Teltonika device. The decoded readings include temperature, humidity, light level, movement, tilt, battery, and fuel level.

Navixy keeps each tag's readings apart from the GPS device's own readings. For example, the readings of a temperature tag in a cargo area are stored for that tag, not as readings of the GPS device that forwarded them.

Some GPS devices forward BLE readings only in a specific data format. For Teltonika devices, see [Codec 8 Extended for BLE sensors on Teltonika devices](https://app.gitbook.com/s/iUOsBLCF28U0Cm4ggUHo/devices/teltonika/codec-8-extended-for-ble-sensors).

## See also

* [OBD device fundamentals](../can-and-obdii/obd-tracker-fundamentals.md): Learn how plug-and-play OBD devices read vehicle data and connect to Bluetooth accessories.
* [Fuel level sensors](../fuel-management/installation-and-initial-configuration-of-fuel-control-devices/fuel-level-sensors/README.md): Compare the types of fuel level sensors and learn how to install them.
