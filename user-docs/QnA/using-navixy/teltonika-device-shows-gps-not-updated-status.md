---
title: Teltonika device shows "GPS not updated" status
description: "Device operates in static navigation mode; review the relevant parameters and voltage thresholds to resume GPS position updates"
---

# Teltonika device shows "GPS not updated" status

## Question

Why is my Teltonika device showing "GPS not updated" and 0 satellites in last messages in the Air Console, even though it has GSM coverage?

![](<../.gitbook/assets/Unknown image (1)>)

## Answer

It looks like the tracker operates in the "Static navigation mode". While the device can still connect to cellular networks (GSM), it doesn't update its GPS location in this mode.

First, let's understand static navigation: Static navigation is a power-saving feature that helps prevent GPS errors like location drift or spoofing. When your device determines it isn't moving or ignition is off (based on your settings), it stops taking new GPS readings to avoid inaccurate positions that can happen with weak satellite signals. Think of it like putting your GPS to sleep when your vehicle is parked.

The mode can be triggered by:

* Movement detection
* Vehicle ignition status
* Both movement and ignition

For movement detection, the device can use:

* Ignition status
* Accelerometer readings
* GNSS location changes
* CAN bus speed data

For ignition detection, it can use:

* Discrete input 1 (typically wired to your car's ignition)
* Accelerometer data
* Power voltage range (low to high voltage)
* CAN bus engine RPM

Here's how to diagnose the issue:

1. First, check these device parameters using the command: `getparam 106; 112; 138; 102; 101`
2. Check the responses based on the device's Wiki: [https://wiki.teltonika-gps.com/view/FMB920\_Parameter\_list](https://wiki.teltonika-gps.com/view/FMB920_Parameter_list)
   * If you see `106:1`, static navigation is enabled
   * If you see `112:1`, movement is determined by motion detection
   * If you see `138:1`, movement source is linked to ignition
   * If you see `101:4`, ignition is determined by power voltage
3. Then check voltage settings with: `getparam 105;104`
   * Parameter 105 shows the minimum voltage (currently 13000mV or 13V)
   * Parameter 104 shows the maximum voltage (currently 30000mV or 30V)
4. Review your device's voltage history:
   * Check when the last valid GPS position was received in the location widget.

        ![](<../.gitbook/assets/Unknown image (2)>)
   * Look at the board voltage sensor measurements within the measuring sensors report.

        ![](<../.gitbook/assets/Unknown image (3)>)
   * Compare the timestamps of the last GPS update with the last time voltage was above the minimum threshold.

Current situation: The device is in static navigation mode because the power voltage is below 13V. The device interprets this as the vehicle being off, so it stops updating GPS positions. The last GPS update was at 10:39 PM, which matches the last time the voltage was above 13V.

To fix the issue, you can:

* Disable static navigation completely
* Adjust the movement source to something other than ignition, for example accelerometer
* Modify the voltage thresholds to match your vehicle's normal operating range
* Check your vehicle's electrical system if voltage is consistently low
* Change how static navigation is determined (movement vs. ignition vs. both)

## Links

[Measuring sensors report](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/measuring-sensors-report)

[Teltonika Wiki for one model](https://wiki.teltonika-gps.com/view/FMB920_Parameter_list)

[Static navigation feature explanation](https://wiki.teltonika-gps.com/view/FMB920_System_settings#Static_Navigation_Settings).