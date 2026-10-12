---
description: "Configure fuel level sensors in Navixy: set calibration data, accuracy, smoothing, and tank details for reliable fuel monitoring and consumption analysis."
---

# Fuel level sensor

Fuel level sensors come in various types, and each serves a particular purpose. You can find detailed information about them in the [Expert Center](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management). This page covers the fuel operation of the Navixy platform, the fuel sensor settings, and their impact on the overall fuel management process.

## What the fuel sensor readings depend on

The accuracy of fuel data displayed is dependent on several factors, including:

* **Fuel level sensor**: What kind and how well it is able to read from the tank, and how well it interacts with the GPS device.
* **Tank sensor installation**: If you have a non-standard tank or require a non-standard installation, it is best to consult with the manufacturer on how to install such a sensor.
* **Device-side settings**: Make sure that the sensor is configured and the device sends its data to the Navixy platform. It is desirable not to do additional calibrations on the device side, but to do it directly on the platform.
* **Platform-side settings**: Widget readings depend on them, as do draining and refueling alerts and fuel level reports. By optimizing these settings, it becomes easier to identify anomalies and irregularities in fuel data readings that may require further attention.

## Sensor creation

Fuel sensors are a type of [Measurement sensor](./). To create a fuel sensor, navigate to **Devices and settings** and scroll down to the **Sensors and buttons** panel. Next, click the **+** icon and select **Measurement sensor** from the dropdown menu.

## Fuel sensor settings

Once you choose **Fuel level** as the sensor type, additional settings appear. The complete list of options for the fuel sensor includes:

* **Sensor name**: Assign a clear and convenient name for the sensor. This name is visible on widgets, reports, and rules to help you easily identify the sensor.
* **Input**: Select the input from which the device transmits fuel data.
* **Units**: Select a unit of measure.
* **Accuracy**: This refers to the specified percentage used to calculate the absolute error in tank volume. This error value is used to compute the amount for refills and drains.
* **Thresholds for drain detection:** Used to determine drains in fuel reports. This parameter can be represented as the rate of change in the fuel level. Both thresholds are always checked, and if the fuel level changes faster than at least one of the set thresholds for more than the accuracy level, the report marks the fuel drain.
  * **By time**: The maximum allowable rate of fuel drop (how fast the level can fall) is measured in units per hour. When calculating this over time, the platform compares the fuel level change between data points. If not set, the default is 120 units per hour. This means a drain is flagged only when the level drops faster than 120 units per hour (equivalent to 20 L per 10 minutes, or 2 L per minute). Set this a few percentage points above the likely consumption rate during heavy loads or uphill driving.
  * **By mileage**: The maximum allowable rate of fuel drop is measured in units per 100 km. For example, if you set 100 L per 100 km, a drain is flagged only when the level drops faster than 100 L per 100 km (equivalent to 10 L per 10 km, or 1 L per km). Enter this value based on real-world tests rather than the manufacturer’s stated fuel consumption. Generate reports, verify actual rates, and adjust accordingly.
* **Ignore in movement**: The platform automatically excludes any drains and refills that occur during movement from rules and reports. Movement is determined by the [Parking Detection setting](../../location-and-movement/parking-detection.md).
  * **Drains:** Drains in movement are excluded.
  * **Refills:** Refills in movement are filtered.
  * **Filter timeout:** This setting appears when the Ignore feature is enabled. It determines the timeout period in minutes that is used to shorten the driving intervals for fuel filtering. This option can be helpful if the fuel level stabilizes only after some time has passed since refueling, and the vehicle has already started moving. This is more commonly seen in vehicles with large fuel tanks. The default setting for this feature is 5 minutes.
* **Calibration table**: This parameter is used to convert the sensor readings into desired units such as liters. Some sensor manufacturers may provide the conversion values for the table. However, in most situations, calibration is necessary to achieve accurate readings.
  * **Tank volume** is the maximum volume of the tank, which is specified in units in the calibration table. If calibration values are not specified, the default value of 100 is assumed, which indicates that the data is being transmitted in percentage.
  * Even if your sensor already sends data to the platform in liters, set the calibration to 0 = 0 liters and maximum fuel tank capacity = X liters.
  * If it's a sensor that transmits fuel level information in percentages, set calibration 0 = 0 liters and 100 = maximum fuel tank capacity in liters.
* Advanced settings are below the calibration table.
  * **Ignore values:** Values should be specified the same way they come to the platform from the device.
    * **Less**: the filter can be used to ignore any readings that fall below a certain threshold, X. This is helpful in situations where a sensor's readings may fall below a certain value. For example, a loose wire or a sensor that sends a reading of 0 when the ignition is switched off.
    * **More**: the filter can be used to ignore any readings that exceed a certain threshold, X. This is valuable when dealing with sensors whose readings may occasionally increase dramatically. For example, if an error is detected or if there is a higher voltage than expected.
  * **Multiplier**: Multiply the resulting values by a certain coefficient. If you want to divide values, use decimals.

## How fuel works on the platform

### Receiving and processing fuel data

The platform reads and saves fuel sensor readings as they are received from the devices. Data from fuel sensors is only saved once a fuel sensor has been created in the system.

Filters for minima and maxima, calibration tables, and other sensor settings are only applied when the data is being output. As a result, you can change the settings at any time and rebuild the reports to see how the changes have affected the saved data. This provides flexibility in terms of configuration and ensures that the data is always up-to-date with the latest settings.

### Drains and refills in rules

Rules for draining and refueling are based on the calibration table and the absolute error, which equals tank volume multiplied by the accuracy percentage.

The platform records the last current reading of the sensor for ten minutes. Based on this reading, the following events are triggered:

* If the fuel level has increased by more than the absolute error, a "filling" event is recorded.
* If the fuel level has decreased by more than the absolute error, a "draining" event is recorded.

For instance, if the tank capacity is 100 liters and the accuracy is 5%, a 5-liter change in fuel level within a 10-minute timeframe triggers the rule.

### Refills and drains in reports

The reporting system provides a more advanced method of analysis as it relies on a larger pool of saved data. All parameters are taken into account in the report analysis.

The platform uses the rate of fuel level decrease and absolute error to identify and register fuel drains. A "drain" occurs when the fuel level decreases by more than the absolute error within the specified thresholds for drains by time or mileage (if set).

In the report, a "filling" event is recorded when the fuel level increases by more than the absolute error. The platform groups consecutive filling or draining events, meaning that if the same condition is triggered repeatedly, the platform groups them into a single large filling or draining event.

### Ignoring refills and drains in motion

Once you have enabled one or both options for ignoring, the next algorithm is used for reports and alerts in addition to the standard:

* If refueling begins during the [parking time](../../location-and-movement/parking-detection.md), it is displayed in the report and logged by the rule. Additionally, if refueling starts within X minutes of the filter timeout before parking or within X minutes of the filter timeout after the trip starts, it is also logged by the rule and shown in the report.
* However, if refueling starts earlier than X minutes from the filter timeout before parking or later than X minutes from the filter timeout since the beginning of the trip, it is filtered out.
* In cases where no filter timeout is specified, all refills that start during trips are filtered.

### Fuel sensor readings analysis and classification

The application of various statistical methods has resulted in a model for analyzing the quality of sensor data. The Navixy team has developed an adaptive algorithm that can classify the quality of raw sensor data readings and provide a score on a 10-point scale: from 1 (the lowest quality) to 10 (high-quality data).\
To try this feature, you need to generate a [fuel volume report](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management/fuel-control-in-navixy/analyzing-fuel-data/fuel-volume-report) for the object(s) under investigation. The quality rating is available at the bottom of the report both in the interface of the cabinet and in documents (PDF/XLS) downloaded based on the generated report. The rating can also be obtained using API requests.

## See also

* [Enhancing fuel management accuracy with Fuel Sensor Quality Index](https://www.navixy.com/blog/enhancing-fuel-management-accuracy-with-fuel-sensor-quality-index/)
* [Mastering calibration tables for accurate fuel management](https://www.navixy.com/blog/calibration-tables/)
* [Sensors setup and configuration](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management/fuel-control-in-navixy/sensors-setup-and-configuration)
