---
description: "Three Wialon protocols supported by Navixy for IoT data ingestion: connection parameters, supported inputs, event types, and message formats for each."
---

# Wialon protocols for data ingestion

Navixy supports the [integration of IoT and Telematics data](./) from various platforms and gateways using various protocols. These protocols allow seamless IoT data transmission from devices and sensors to the Navixy platform, enabling comprehensive data collection and analysis.

This document provides an overview of the suite of Wialon protocols, detailing how they work and how to configure them for use with Navixy.

## Wialon Retranslator

The Wialon Retranslator protocol is designed for retransmitting data via TCP in binary format. This protocol allows for the transmission of information about a device's location and sensor values. It can be extended to support specific fields based on the device model, enabling detailed data collection.

**Supported Inputs**:

* AVL IO: #1-100000
* Battery voltage
* CAN: Axle load, Level of AdBlue fluid, RPM, Total fuel used, Engine temperature, Mileage, Raw data
* LLS: Level
* Temperature
* Analog sensor: #1-16
* Board voltage
* External temperature: #1-10

**Supported Events**:

* Deviation from the route
* Distance between objects
* Engine excessive idling
* Entrance or exit from geofence
* Excessive driving
* Excessive parking
* Fuel level change
* GPS antenna cut
* GPS signal lost/recovered
* GSM jamming
* Inputs/outputs triggering
* Parameter in range
* Parking state detection
* Pressing SOS button
* Shake sensor
* Speeding
* Task status change
* Tracker switched OFF or lost connection

**How to Connect**:\
To add a device to Navixy using the Wialon Retranslator protocol, register a new device with its ID on Navixy and specify the following tracking server IP address and port on the sender's side:

* **EU Platform**: `tracker.navixy.com` or `52.57.1.136`
* **US Platform**: `tracker.us.navixy.com` or `13.52.37.2`
* **Server port**: `47690`

## Wialon Combine

The Wialon Combine protocol retransmits data via TCP or UDP in binary format. It supports transmitting a wide range of telematics and telemetry data, with the platform supporting all custom fields from the protocol.

**Supported Inputs**:

* Analog sensor: #1-32767
* Counter: #1-32767
* External temperature: #1-32767
* CAN Parameter: #1-32767
* Custom parameter: #1-32767
* LLS: Level: #1-32767

**Supported Events**:

* Deviation from the route
* Distance between objects
* Engine excessive idling
* Entrance or exit from geofence
* Excessive driving
* Excessive parking
* Fuel level change
* Inputs/outputs triggering
* Parameter in range
* Parking state detection
* Pressing SOS button
* Speeding
* Task status change
* Tracker switched OFF or lost connection

**How to Connect**:\
To add a device to Navixy using the Wialon Combine protocol, register a new device with its ID on Navixy and specify the following tracking server IP address and port on the sender's side:

* **EU Platform**: `tracker.navixy.com` or `52.57.1.136`
* **US Platform**: `tracker.us.navixy.com` or `13.52.37.2`
* **Server port**: `47645`

## Wialon IPS

The Wialon IPS protocol is a text-based protocol over TCP, designed to be easily extended by passing additional parameters in the `#D` message.

**Message Format**:

```
#D#date;time;lat1;lat2;lon1;lon2;speed;course;height;sats;hdop;inputs;outputs;adc;ibutton;params\r\n
```

**Params** is a comma-separated set of additional parameters, each with a `NAME:TYPE:VALUE` structure.

**General Parameters**:

* SOS - SOS button alarm
* INTERVAL\_MODE - single-point route sign
* BatteryLevel - battery charge in percent
* b - battery charge in Volts
* a or LOC\_RADIUS - location accuracy (radius) in meters
* LOC\_SRC - navigation data source: 0 - GPS, 1 - GSM LBS
* pwr\_ext - board voltage
* pwr\_int - built-in battery voltage
* fuel level - fuel level in liters
* temperature - temperature from the built-in sensor

**Specific parameters** **for BITREK devices**:

* ADC0, ADC1 - values of analog sensors 1 and 2 in millivolts
* AIN1, AIN2 - values of analog sensors 1 and 2 in volts
* DIN1, DIN2, DIN3, DIN4 - discrete inputs values
* VBAT - built-in battery voltage
* par1, par2, ... - BITREK protocol parameters corresponding to IO element codes

**How to Connect**:\
To connect a device using the Wialon IPS protocol, add the device to Navixy as one of the BITREK models or other supported models like Glonassoft UMKa and NaviTrek. Specify the device ID during registration.
