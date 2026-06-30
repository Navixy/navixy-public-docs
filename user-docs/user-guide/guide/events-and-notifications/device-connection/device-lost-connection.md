---
description: Get notified when a GPS device goes offline beyond your configured timeout. Covers the Offline time setting, reset behavior, and supported device combinations.
---

# Device lost connection

The **Device lost connection** rule is designed to monitor and notify users when a GPS device loses its connection with the Navixy platform. This loss of connection can occur due to several reasons, such as hardware issues (battery discharge, power disconnection, hardware failure, wiring problems) or communication issues (GSM network problems, poor coverage, insufficient funds, or provider outages).

This rule helps users stay informed about the operational status of their tracking devices and enables quick responses to potential issues, such as equipment malfunctions, GSM jamming, or vehicle theft. It also aids in collecting valuable data for optimizing operations, such as identifying areas with poor coverage or detecting faults in the equipment or service provider.

{% hint style="info" %}
For real-time monitoring of device power status, use the [Device switch ON/OFF](../device-power/device-switched-onoff.md) self-reporting feature if it's supported by your GPS device.
{% endhint %}

## Rule settings

### Offline time more than

The **Offline time more than** setting allows users to define a custom timer that starts counting down after the GPS device goes into a red status (indicating loss of connection). The total disconnection period is calculated as follows:

- **Default timeout (10 minutes)**: This is the standard period after which a GPS device is considered to have lost connection if no data is received from the device.
- **Your custom offline timeout**: The additional time specified by the user in the **Offline time more than** field.

For example, if a device loses connection at 10:10 AM, after the default timeout of 10 minutes, it is marked with a red status at 10:20 AM. If the user sets the custom offline timeout to 7 minutes, the notification is triggered at 10:27 AM.

For common rule settings, see [Rules and Alerts](../README.md).

## System operation details

- The **Device lost connection** alert has a 10-second reset timer, which means the alert doesn't trigger more often than once every 10 seconds. If an event occurs while the rule is waiting for the reset, it is omitted by the platform, including in reports.
- This rule is processed in the cloud and is not tied to any specific hardware, making it applicable to multiple GPS devices simultaneously. This flexibility allows you to manage several GPS devices within a single rule.
- Different device models have varying transport-level timeouts. If you include multiple GPS device models in the same rule, the notification periods may differ based on the device's specific timeout settings. This variation occurs because the notification is triggered by a combination of the GPS device's default timeout and the custom offline timeout specified in the rule configuration.

By using this rule, users can ensure they are promptly informed of any disruptions in GPS device connectivity, enabling them to take swift action to address potential issues.
