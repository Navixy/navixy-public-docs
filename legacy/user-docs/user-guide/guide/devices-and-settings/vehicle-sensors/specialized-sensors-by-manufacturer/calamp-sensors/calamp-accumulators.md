---
description: Map CalAmp accumulator variables in Navixy to retrieve board voltage and external temperature data from supported CalAmp GPS devices.
---

# CalAmp accumulators

[CalAmp devices](https://www.navixy.com/devices/calamp/) are equipped with accumulators (variables) that store various types of data, depending on how the device is configured by the user.

To streamline the integration and usage of these accumulators within the Navixy platform, a dedicated block has been introduced. This block allows users to easily configure and retrieve data from the following accumulators:

## Supported accumulators

* **Board voltage**: Monitors the voltage level of the device's internal board, which is crucial for assessing the health and power status of the device.
* **External temperature sensors (1-8)**: Captures data from up to 8 external temperature sensors connected to the CalAmp device, enabling detailed monitoring of environmental conditions.
* **Hardware mileage**: This is the mileage data calculated directly by the device itself, providing an accurate measure of distance traveled based on hardware input rather than GPS data alone.
* **Analog sensor values (1-8)**: Retrieves readings from up to 8 analog sensors connected to the device, allowing for a wide range of inputs to be monitored, such as pressure, humidity, or other analog signals.
* **IO\_States (Input/Output States)**: Displays the status of the device’s input and output channels.
* **iButton ID (Low and High Parts)**: Captures the unique identifier of iButton devices in two parts (low and high), which is typically used for driver identification or access control in fleet management applications.

## Configuring CalAmp accumulators in Navixy

To set up and monitor these accumulators on the Navixy platform, follow these steps:

1. Open the **Devices and settings** module.
2. Select the desired CalAmp device.
3. Open the **Accumulators** block. You can select which accumulators you want to monitor. Depending on your requirements, you can enable the monitoring of board voltage, external temperature sensors, hardware mileage, analog sensor values, IO states, and iButton ID parts.
4. Save your configuration.

## Monitoring accumulator data

Once configured, you can view real-time data from the selected accumulators directly within the Navixy platform. This data can be used for various monitoring and reporting purposes, depending on your operational needs.

## Practical applications

* **Fleet management**: Monitor hardware mileage for accurate distance tracking and maintenance scheduling.
* **Environmental monitoring**: Use external temperature sensors for tracking environmental conditions inside cargo or around vehicles.
* **Driver identification**: Implement iButton ID monitoring to ensure that only authorized personnel operate your vehicles.
* **Analog input monitoring**: Track various analog inputs for specialized applications, such as monitoring fluid levels or pressure in tanks.
