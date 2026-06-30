---
description: Set up Galileosky and PP-01 sensor integration in Navixy to log real-time passenger entry and exit counts for transit vehicles and ridership reporting.
---

# Passenger counter (Galileosky)

[Galileosky devices](https://www.navixy.com/devices/galileosky/), when integrated with PP-01 passenger flow sensors, count the number of passengers entering and exiting public transportation vehicles. This integration leverages Galileosky’s Easy Logic technology, enabling data collection and analysis through the Navixy platform.

## Example use case

City transit operators use this integration to monitor passenger flow, analyze ridership trends, and adjust routes based on actual load data.

## Key features and benefits

* **Passenger counting:** The PP-01 sensor connected to the Galileosky device allows accurate counting of passengers entering and exiting the vehicle.
* **Event-based data logging:** Data can be logged based on specific events such as the opening or closing of doors, ensuring that passenger data is recorded only during relevant times.
* **Cumulative data recording:** Navixy can also be configured to log cumulative passenger data, providing a running total of passengers throughout the trip.

## Integration steps

1. **Connect the PP-01 Sensor:**

* The PP-01 sensor connects to the Galileosky device via the RS485 interface. Ensure proper connection following the wiring instructions provided.
* Configure the RS485 interface with the following parameters:
  * Speed: 19200 bits/s
  * Data bits: 8
  * Stop bit: 1

2. **Configure the PP-01 Sensor:**

* Connect the PP-01 sensor to a PC using an RS485-USB adapter (a small converter that lets you plug an RS485 industrial sensor into a standard USB port on your computer).
* Use the PP-01 Configuration Utility to set up the sensor. Adjust the sensor’s address as needed, choosing from the range of 1 to 8.
* Use the Galileosky Configurator to load the appropriate Easy Logic script (a small program that runs on the device and defines how passenger events are counted and reported) depending on your chosen mode (event-based or cumulative recording).
* Ensure that the script is successfully uploaded and configured on the Galileosky device.

3. **Integration with Navixy:**

* In the Navixy platform, navigate to **Devices and settings** and go to **Sensors and buttons**.
* Create a new measurement sensor and select the input corresponding to the user tag from the Galileosky device.
* Map the data from the PP-01 sensor to the Navixy platform, ensuring correct correspondence between the sensor data and the Navixy interface.

## Monitoring and reporting

* **Real-time view:** Once integrated, the passenger data can be monitored in real-time using the **Sensor readings** block in [Object widget](../../../../tracking/objects-list/object-widget.md).
* **Reports:** You can also generate detailed [Reports](../../../../reports/specific-report-details/measuring-sensors-report.md) that include passenger count data, allowing for comprehensive analysis and insights into passenger flow.
