---
description: Alert when a vehicle exceeds a speed limit using GPS data analyzed by the platform, or based on events reported directly by the GPS device.
---

# Speeding

The purpose of the speeding detection rule is to help businesses enhance their overall safety measures by monitoring and addressing speeding events. This ensures compliance with speed limits, promotes responsible driving behavior among employees, and reduces the risk of accidents. The Navixy platform offers two methods for detecting speeding:

## Method I. platform-detected speeding

This method relies on speed data from GPS devices to identify speeding. The platform analyzes the speed data from GPS data packets to check if the specified speed limit has been exceeded. While this method is less accurate than device-detected speeding, it can be used with any GPS device that reports speed. It is a suitable option for devices that can't natively detect speeding events.

## Method II. hardware-detected speeding

This method uses the hardware of the GPS device to detect speeding. The device itself calculates when a speeding event occurs using remote commands or device configuration software. The platform then receives notifications from the device based on these calculations. This method is highly accurate and works with most modern GPS devices that can send speeding events directly to the platform.

## Rule settings

### Speed limit, platform-detected speeding

The speed limit setting defines the threshold at which the rule is triggered. When the platform receives speed data from the device, it compares this value to the specified limit. If the speed exceeds the limit, the platform generates a speeding alert.

### Speed limit, hardware-detected speeding

For device-detected speeding events, there is no specific setting in the Rules and Notifications. Instead, use the Device Settings widget to remotely configure the speed limit on the device side.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Hardware requirements and event processing**
  * **Speeding Platform-detected speeding**: This rule can be applied to most GPS devices that send speed data in their packets. The rule is processed in the cloud, where the platform uses the speed data from the device packets to determine if the speed limit has been exceeded. The alert has a 15-minute reset timer, meaning the alert won't trigger more often than once every 15 minutes. If an event occurs while the rule is waiting for the reset, the platform omits the event, including in reports.
  * **Speeding Hardware-detected speeding**: This rule is only applicable to GPS devices that support sending hardware-based speeding events. The device itself processes the rule, and the platform generates notifications based on the calculations made by the hardware. The alert has a 1-minute reset timer, meaning the alert won't trigger more often than once every minute. If an event occurs while the rule is waiting for the reset, the platform omits the event, including in reports.
* **Multiple GPS devices**: The rule allow for the selection of multiple GPS devices within a single rule, enabling comprehensive monitoring across various vehicles or devices.
* **GPS drifting**: The system may generate a speeding alert even if GPS drifting occurs. While invalid GPS coordinates are filtered, small GPS drifts can still appear in the track. However, the platform offers various methods to minimize these occurrences, depending on the GPS device model’s functionality. For detailed information on preventing GPS drifting, see the device manual. This ensures more accurate and reliable event detection, enhancing overall monitoring effectiveness.
