---
title: Platform shows a full fuel tank when it is not actually full
description: "Check the fuel sensor connection and calibration table; error codes 65454 or 65455 in raw data indicate a sensor communication failure"
---

# Platform shows a full fuel tank when it is not actually full

## Question

Why does the platform show a full fuel tank when it is not actually full?

## Answer

There's likely a sensor connection problem. Here's what's happening:

When your fuel sensor is working correctly, it should send values between 0 and last calibration table value, where last calibration value represents a full tank. However, your device is sending values higher than the last calibration table value: 65454 and 65455, which are error codes. These abnormal readings typically indicate one of two problems:

1. The sensor has disconnected from the tracker
2. The sensor itself has failed

Here is how to diagnose this issue:

1. Check when the sensor was last updated. Hover your mouse on the sensor name and wait for a few seconds. The last update of the sensor will be visible.
2. Check the sensor’s calibration table on the platform. It should be configured properly.
3. Check the last received values from the sensor’s input using the Air Console or Raw Data export for the last several minutes/hours.

To fix this:

* Check the physical connection between the sensor and tracker (if it is a wired sensor) and sensor presence if it is a BLE sensor.
* Test the sensor itself to see if it's functioning.
* If needed, replace the faulty sensor or repair the connection.

## Links

[Sensor calibration data](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/vehicle-telematics-technology/fuel-management/fuel-control-in-navixy/sensors-setup-and-configuration)

[Air Console instruction](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/devices/air-console)

[Getting raw data](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file#raw-data-export)
