---
description: Monitor GPS device output state changes such as engine block activation or alarm engagement. Receive alerts whenever a configured output switches on or off.
---

# Output triggering

Vehicle GPS devices often come equipped with configurable outputs that enable users to control various vehicle functions, such as engine immobilization, alarm activation, and more. These outputs can be managed directly from the dashboard in your telematics platform. By configuring a rule for output state changes, you can receive notifications via the user interface, mobile app, SMS, or email whenever there is a modification in the output's status.

This feature ensures you remain informed about any changes to the equipment connected to your GPS device. Whether you’re activating or deactivating specific functions, this rule provides a reliable way to monitor and manage the status of your vehicle outputs.

For example, in fleet management, this rule is particularly useful for monitoring the status of an engine block or fuel cut-off system. If a GPS device is configured to control these outputs, the rule can generate alerts whenever the engine block is activated or deactivated. This allows fleet managers to ensure that security measures are being properly applied and that unauthorized vehicle use is prevented.

## Rule settings

### Output

Specify the hardware output you want to monitor by selecting the appropriate output number. Refer to the GPS device manufacturer's documentation to determine which output controls which functionality.

## System operation details

- **GPS-independent event alert:** If the Navixy platform detects an output change event (such as a switch from 1 to 0 or vice versa) from a GPS device data packet without valid coordinates, it still counts the event as valid and displays it. This applies regardless of whether the event occurred inside or outside a geofence, as the Inside/Outside geofence logic is ignored to ensure potentially important events are not overlooked.
- **Selective alerts:** If you want to receive alerts only for specific output changes, such as from "OFF to ON" and not from "ON to OFF," you can achieve this by clearing the notification text for the undesired output state in the settings.
- **Reset timer:** The **Outputs triggering** alert has a 10-second reset timer, meaning it doesn't trigger more frequently than once every 10 seconds. If another event occurs during the reset period, it is omitted from the platform, including in reports.
- **Multiple devices:** You can assign multiple GPS devices to this rule, with each GPS device using the same output number specified in the rule settings. For example, if you select output #2, the rule notifies you whenever any of the selected GPS devices reports a change in its output #2.
