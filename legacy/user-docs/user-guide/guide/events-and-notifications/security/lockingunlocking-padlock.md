---
description: Receive real-time notifications when a GPS-enabled smart lock is locked or unlocked. Monitor secure areas and cargo containers for all lock interactions.
---

# Locking and unlocking padlock

The **Locking/Unlocking padlock** rule is essential for organizations using GPS-enabled smart locks to secure valuable assets and critical areas. This rule monitors the status of these locks, ensuring that users are instantly notified whenever a lock is engaged or disengaged.

Specifically designed for GPS locks, this rule provides real-time alerts for all lock interactions, whether securing an area or locking cargo in a container.

## Rule settings

This rule is entirely dependent on the device's capabilities and hardware configuration. There are no specific settings to configure within the rule itself.

For common settings, see [Rules and Alerts](../README.md).

## System operation details

* **Reset timer:** The **Locking/Unlocking padlock** alert has a 1-minute reset timer, meaning it doesn't trigger more frequently than once every minute. Events occurring during this reset period are omitted from notifications and reports.
* **Multiple devices:** This rule supports multiple GPS devices, provided they can detect Locking/Unlocking (Padlock) events and have the feature integrated into the Navixy platform. Users can monitor these events across various devices efficiently.
* **GPS-independent processing:** The platform processes and displays Locking/Unlocking events even if the data packet lacks valid GPS coordinates. These events are recorded regardless of whether they occur inside or outside a geofence, ensuring no critical event is missed.
