---
description: >-
  Track the distance between fleet assets and receive alerts when objects
  approach, move away, or exceed a defined separation range, from 5 to 200,000
  meters.
---

# Distance between objects

The "Distance between objects" rule is designed to help you effectively manage and monitor the distance between various assets, such as between two vehicles, a vehicle and its cargo, or a truck and its trailers.

This rule allows you to set specific distance thresholds between tracked objects. If the distance exceeds or falls below the set limits, an event is triggered, recorded, and can generate a notification.

## How it works

This rule leverages GPS data to continuously track the distance between a designated primary object, such as a lead vehicle, and up to 100 secondary objects, such as trailers or following vehicles. By setting specific distance parameters, users can receive real-time notifications when these objects move closer together or farther apart than allowed. The system automatically calculates distances based on the most recent GPS coordinates, ensuring accurate and timely alerts.

## Applications

* **Trailer and asset tracking:** Maintain control over the distance between a vehicle and its attached or following assets, such as trailers. This is essential for preventing accidental disconnection, managing convoy spacing, and ensuring that trailers remain within a safe operational range.
* **Safe following distance enforcement:** Monitor and enforce safe following distances between fleet vehicles. This application is crucial for preventing collisions and promoting adherence to safety protocols, particularly in high-traffic environments or when operating heavy-duty vehicles.
* **Fleet coordination and traffic management:** Use the rule to manage the spacing of vehicles in a convoy or to monitor the relative positions of multiple fleet assets during operations. This data can also be aggregated for traffic analysis, helping to identify congestion patterns and optimize route planning.

## Rule settings

### Distance parameters

* **Approaching:** Configure the system to alert you when secondary objects are moving closer to the primary vehicle. This setting is critical for maintaining safe operating distances and preventing collisions or other safety hazards.
* **Moving away:** Set alerts for when secondary objects are increasing their distance from the primary vehicle. This is particularly useful for detecting when vehicles are straying from a convoy or when trailers are detaching from their lead vehicle.
* **Distance range:** Define a specific permissible distance range between the primary and secondary objects, ranging from 5 to 200,000 meters. The system generates notifications whenever the distance falls within or outside this predefined range, allowing for proactive management of fleet operations.

![](<../../../.gitbook/assets/image-20240813-220927 (1).png>)

For common settings, see [Rules and alerts](../).

### System operation details

* **GPS-based distance calculation:** The rule calculates distances based on the latest available GPS coordinates, ensuring accuracy in monitoring object proximity.
* **Reset timer:** The **Distance between objects** alert features a 10-second reset timer, meaning alerts won't trigger more frequently than once every 10 seconds. This helps prevent redundant notifications and ensures that alerts are meaningful and actionable.
* **Multiple devices:** This rule is hardware-agnostic and can be applied to multiple GPS devices simultaneously, offering flexibility in managing large fleets with diverse assets.
