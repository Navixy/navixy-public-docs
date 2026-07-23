---
description: >-
  Receive alerts when a discrete GPS device input changes state, such as a door
  sensor opening, ignition turning on, or an emergency button being pressed.
---

# Input triggering

Input triggering (or, input change) refers to a change in the status of an input on a vehicle tracking device. This input can be connected to various sensors or switches in the vehicle, such as door sensors, ignition status, or other vehicle components. When the input changes state, for example, from on to off or vice versa, the GPS device detects this change and can trigger an event or notification accordingly.

{% hint style="info" %}
The input change type of the rule applies to discrete inputs that take two logical positions (0 or 1). For inputs indicating continuous values (e.g., analog inputs showing a voltage range), there is a specific rule type called Parameter in Range.
{% endhint %}

## Overview

Many vehicle GPS devices come equipped with inputs that can be connected to different tools, such as a car GPS device opening sensor, ignition status, or an emergency button. By configuring the system accordingly, users can receive notifications for input triggers through the user interface, push notifications, SMS, or email.

The rule is designed to monitor specific discrete inputs and notify users whenever there is a change in their status. These discrete inputs can only have two values: 0 or 1, hence the term "discrete." The rule waits for the specified input to transition from true (1) to false (0), or vice versa, and generates notifications for each change detected.

This functionality enables users to stay informed about the status of connected tools or sensors, providing real-time updates on various events, for example, activation of an SOS button. The ability to receive timely notifications enhances monitoring capabilities and facilitates quick responses to critical situations or events detected by discrete inputs.

![](../../../.gitbook/assets/image-20240805-213834.png)

## Rule settings

### Input number

The input number field specifies the hardware or virtual hardware number of the input being used. To determine which input corresponds to a specific functionality, refer to the GPS device’s manufacturer’s documentation. The input number should match the discrete sensor created in the **Device and settings** → **Sensors and buttons** block.

![](../../../.gitbook/assets/image-20240808-190122.png)

## System operation details

* **Reset timer**: The **Inputs change** alert has a 10-second reset timer, ensuring that the alert doesn't trigger more often than once every 10 seconds. If an event occurs during this reset period, it is omitted by the Navixy platform, including in reports.
* **Multiple GPS device assignments**: Multiple GPS devices can be assigned to a single rule. The specified input number in the rule settings is used as a data source for each GPS device. For example, if the 2nd input is chosen and multiple GPS devices are selected, the rule notifies the user whenever any of the selected GPS devices send a change in their input #2.
* **Event validity**: If the platform identifies an input event (1 true/high level or 0 false/low level) from a packet of GPS device data with no valid coordinates, it counts the event as valid and displays it, regardless of whether the event occurred within or outside the bound geofences. The logic of the Inside/Outside radio buttons is ignored in this case, as displaying a controversial event once more is better than omitting it.
* **Selective alerts**: Users can choose to receive input-triggering alerts only for specific changes in input (e.g., from 1 to 0 or from 0 to 1) by clearing the notification text for the respective field they don't want alerts for. For instance, users can configure alerts to receive notifications only for changes from "ON to OFF" while ignoring events from "OFF to ON."

![In this example, the notification text for field #1 is empty](../../../.gitbook/assets/image-20240805-213731.png)
