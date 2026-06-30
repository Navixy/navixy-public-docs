---
description: Get alerted when a GPS device detects no movement for an extended period. Useful for monitoring parked equipment, cargo in transit, or personal safety.
---

# Movement inactivity

The "Movement Inactivity" alert, also known as the "No Movement" alert, is designed to enhance safety and security. It triggers an alert when no movement is detected for an extended period, relying on GPS devices equipped with accelerometers and the necessary firmware capabilities.

This rule is especially useful in three key areas:

1. **Movable assets**: Applied to equipment such as construction machinery and trailers, the "Movement inactivity" alert helps monitor unauthorized stops or prolonged idling, ensuring assets are in use as intended and preventing potential theft or misuse.
2. **Cargo monitoring**: For cargo in transit, this alert can be crucial in detecting when goods have been left unattended or are at risk, allowing for timely intervention to secure valuable shipments.
3. **Personal safety**: For individuals using personal GPS devices, such as elderly people or lone workers, the "Movement inactivity" alert provides an added layer of protection by signaling a potential emergency when the device detects an unusual lack of movement, prompting a response to ensure the individual's well-being.

## Rule settings

As the "Movement Inactivity" is a device-detected event, there is no specific setting in the Rules and Notifications. Instead, use the Device Settings widget to remotely configure the speed limit on the device side.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Reset timer.** The "Movement inactivity" alert has a 1-minute reset timer, meaning the alert won't trigger more frequently than once every minute. If an event occurs while the rule is waiting for the reset, the Navixy platform omits the event, including in reports.
* **GPS-independent event alert.** The system may generate a "Movement inactivity" alert even if GPS data isn't available. If invalid GPS coordinates are detected, the platform still registers and displays the event, regardless of whether it occurred within or outside the designated geofences. The logic of the Inside/Outside geofence settings is ignored in these cases to ensure critical events are not missed.
