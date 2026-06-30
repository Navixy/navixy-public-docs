---
description: Get notified when a GPS device loses satellite signal and when it recovers, helping you monitor assets in tunnels, warehouses, or low-coverage areas.
---

# GPS signal loss and recovery

The **GPS signal loss / recovery** rule monitors the availability of the GPS signal for your devices. This rule triggers notifications when a device loses its GPS signal, often due to being indoors, underground, or in an area with poor satellite visibility, and when the signal is subsequently regained. By receiving these notifications, you can stay informed about the status of your assets' locations, even in challenging environments.

This rule is particularly useful in environments where GPS signal coverage is unreliable, such as construction sites, warehouses, or underground facilities. Ensuring that your GPS device maintains cellular and internet connections is crucial for receiving these alerts when the GPS signal is lost and recovered.

## Rule settings

This rule is cloud-processed, meaning the server software monitors when the GPS signal is lost and when it is recovered. You can apply this rule to multiple GPS devices simultaneously, as long as the GPS devices support this event type and it is integrated into the Navixy platform.

For common rule settings, see the [Rules and Alerts](../README.md).

## System operation details

* **Reset timer:** The **GPS signal lost/recover** alert has a 10-second reset timer, meaning the alert doesn't trigger more frequently than once every 10 seconds. If an event occurs during the reset period, it is omitted by the platform, including in reports.
* **Multiple devices:** You can select multiple GPS devices to monitor under this rule, as long as the GPS devices support GPS signal lost/recover events and the feature is integrated into the platform. This allows you to manage multiple devices efficiently.
* **GPS-independent event alert:** The platform registers and displays this event even if it is received in a message without valid coordinates, ensuring you are informed regardless of the event's location. The Inside/Outside settings for geofences are ignored in such cases, as the platform prioritizes displaying potentially critical events.
