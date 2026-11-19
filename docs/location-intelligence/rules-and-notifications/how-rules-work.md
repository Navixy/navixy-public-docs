---
noIndex: true
description: >-
  Which GPS device data a notification rule checks, how its equations combine,
  and when Navixy sends a notification.
tags:
  - fundamentals
  - beta
---

# How rules work

A notification rule sends a notification when its equations become true for one of the assets that the rule covers. An equation is one check in a rule, such as a speed over 90 km/h or a position inside a geo object. This page explains which data a rule checks, how its equations combine, and when Navixy sends a notification.

## Which data a rule checks

A rule checks the messages from the GPS devices that are assigned to its assets. A message is one set of data that a GPS device sends, such as its position and speed at one moment. Navixy checks each message when the message arrives, with that message's values.

Navixy checks a GPS device only through the device's asset. A GPS device that isn't assigned to an asset is never checked, so [assign the device to an asset](../bdr/assets/manage-assets.md#assign-a-device-to-an-asset) first. When you replace an asset's GPS device, the rule checks the new device without any change to the rule.

When a rule has no assets selected, the rule covers every asset in the workspace, including the assets that you add later.

When an asset has two GPS devices, Navixy checks each device's messages separately.

Rules check device data after [IoT Logic](../iot-logic/README.md) processes the data. A rule can compare a value that an IoT Logic flow calculates in the same way as a value that a GPS device sends. IoT Logic skips a message that is older than the last message that it processed from the same GPS device. A rule never checks such a message.

## How equations combine

A rule has 1 to 16 equations. Navixy checks the equations in order, from the first to the last. Each equation after the first starts with **and** or **or**, which joins the equation to the result of the equations before it. Navixy doesn't check **and** before **or**, and you can't group equations in brackets.

For example, a rule has these three equations, in this order:

| Order | Joined with | Equation |
|---|---|---|
| 1 | None | The speed is over 90 km/h. |
| 2 | **or** | The time is within the Working hours schedule. |
| 3 | **and** | The position is inside the Depot geo object. |

Navixy checks the first two equations together and then the third equation. The rule is true only when both of these statements are true:

* The asset is inside Depot.
* The speed is over 90 km/h, or the time is within the Working hours schedule.

When the speed equation is last and joined with **or**, the rule is true in either of two cases:

* The speed is over 90 km/h, anywhere.
* The asset is inside Depot, and the time is within the Working hours schedule.

## Parameter equations

A parameter equation compares a value in the message with the value that you enter. For a number, Navixy compares the value in the units that the GPS device reports and doesn't convert units. For example, speed is in kilometers per hour, so a speed value of 90 means 90 km/h. For a duration, Navixy compares lengths of time, so 2 hours and 120 minutes are the same value.

When a message doesn't contain the parameter, Navixy uses the latest value that any of the asset's GPS devices reported for that parameter. Navixy uses this value only when the device recorded the value at most 15 minutes before or after the message. Otherwise, the equation is false. A missing value makes every operator false, including **≠** and **out of range**.

For **in range** and **out of range**, the range includes both end values.

## Geo object equations

A geo object equation checks the message's position against a geo object that covers an area. A rule can use a geo object only when the geo object is one area. Such an area is one polygon or one circle drawn on the map, or a `Polygon` or `MultiPolygon` entered as GeoJSON. A rule can't use a geo object that has two or more drawn shapes. A rule also can't use a geo object that has a point or a line.

| Condition | True for a message whose position is |
|---|---|
| **Inside** | Inside the geo object |
| **Outside** | Outside the geo object |
| **Enters** | Inside the geo object, while the same GPS device's previous position was outside |
| **Exits** | Outside the geo object, while the same GPS device's previous position was inside |

**Enters** and **Exits** are true only for the message that crosses the geo object's border.

For a message without a valid position, Navixy uses the GPS device's last known position. **Inside** and **Outside** use that position, and **Enters** and **Exits** are false.

After you turn on or edit a rule, no geo object equation is true for a GPS device before the device sends a position. **Enters** and **Exits** need a second position, because they compare two positions.

## Schedule equations

A schedule equation checks when the GPS device recorded the message. Navixy compares this time with a [schedule](../bdr/schedules/README.md), in the schedule's time zone. **Within** is true during the schedule's time slots, and **Outside** is true at all other times.

When you turn off a schedule with **Enable schedule**, both **Within** and **Outside** are false for that schedule.

## When a rule sends a notification

A rule is triggered for an asset when its equations become true for at least one of the asset's GPS devices. Navixy sends one notification at that moment. The rule stays triggered while the equations stay true, and Navixy sends nothing more.

The rule is cleared when its equations are false for all of the asset's GPS devices. Navixy sends no notification when a rule is cleared. Navixy sends another notification only after the equations become false and then true again.

Navixy triggers a rule for each asset separately. When the equations become true for two of an asset's GPS devices, the asset gets one notification. When the equations become true for two assets, each asset gets its own notification.

The notification shows the rule's name, the asset's name, and the rule's notification text. When the rule has no notification text, the notification shows the rule's name in its place.

## Data that arrives late

A GPS device that loses its connection stores its data and sends the data when the device connects again. Navixy checks this data in the same way as data that arrives without delay. A rule also sends a notification for late data in the same way. As a result, a notification can describe an event from hours earlier.

## When a change applies

A change to a rule, an asset, a geo object, or a schedule doesn't apply at once.

| Change | When it applies |
|---|---|
| You turn on a rule | About 10 seconds later. The rule doesn't check the messages that arrive before then. |
| You edit and save a rule | From the next message. Navixy checks the edited rule like a new rule. |
| You assign a GPS device to another asset | Within about 1 minute. Until then, rules can still check the device for its previous asset. |
| You change a geo object or a schedule that a rule uses | Within about 1 minute. Until then, rules use the previous shape or time slots. |

When you edit a rule, an asset with two GPS devices can get a second notification. The first message after the edit can [clear the rule](#when-a-rule-sends-a-notification), and a message from the other GPS device can trigger the rule again.

## See also

* [Notification rules page](notification-rules.md): Learn which equations, operators, and covered assets a rule can have, and what its limits are.
* [Schedules](../bdr/schedules/README.md): Learn how schedules and their time slots work.
