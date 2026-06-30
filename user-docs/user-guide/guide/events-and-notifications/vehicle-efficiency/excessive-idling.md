---
description: Alert when a vehicle idles for longer than a set threshold, using platform logic or hardware events. Helps identify fuel waste and unnecessary engine running.
---

# Excessive idling

## Overview

Excessive idling is an important event to monitor in fleet management, as it directly impacts fuel consumption, vehicle wear, and overall operational efficiency. Navixy offers two methods for detecting excessive idling: hardware related and platform related. Each method has its own advantages and is suited to different types of fleet needs.

**The platform related rule** is managed by the Navixy platform and uses parameters set by the user, such as idling duration and parking detection. This rule is ideal for those who need a flexible and customizable solution that works with any GPS device that reports basic data like ignition state and GPS location.

**The hardware related rule,** on the other hand, relies on the GPS device’s built-in capability to detect idling events. The hardware generates and transmits the idling event directly to the platform. This method is advantageous for GPS devices that have advanced idling detection features, offering potentially greater accuracy and additional functionality.

## Rule settings

### Platform related

- **Idling duration:** Set the duration after which idling is considered excessive. The rule monitors when a vehicle is parked (as determined by the Parking Detection settings) and the ignition is ON. If the vehicle remains in this state beyond the specified duration, a notification is triggered.

For common settings, see [Rules and alerts](../README.md).

### Hardware related

- **Device-specific settings:** This rule relies on the hardware's configuration for detecting idling. See your device documentation for information on how to set up idling detection on the device. The platform then receives and displays idling events based on the device’s output.

For common settings, see [Rules and alerts](../README.md).

## System operation details

- **Platform related idling detection:** The platform continuously monitors the vehicle’s ignition state and parking status. When the vehicle is parked with the ignition ON, the platform starts counting the idling duration. If the idling exceeds the user-defined threshold, the system sends an alert. This method works with a wide range of GPS devices and offers flexibility in setting custom idling thresholds.
- **Hardware related idling detection:** The GPS device itself monitors idling and sends an event to the platform when idling exceeds the predefined threshold set in the device. The platform simply forwards this event to the user. This method can offer higher accuracy and advanced features depending on the device’s capabilities.
- **Notifications:** Regardless of the detection method, you receive notifications through the user interface, email, or SMS. These notifications help fleet managers take immediate action to reduce unnecessary idling, thus saving fuel and reducing wear on the vehicles.
- **Reports:** You can generate reports on excessive idling to analyze patterns and make data-driven decisions to improve fleet efficiency. These reports can be particularly useful for identifying habitual idling or assessing the impact of idling on fuel costs and vehicle maintenance.
