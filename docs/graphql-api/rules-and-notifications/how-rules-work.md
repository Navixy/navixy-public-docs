---
description: >-
  Which messages a rule checks, how its equations combine, when a rule activates
  for an asset, and when Navixy sends a notification.
tags:
  - fundamentals
  - beta
---

# How rules work

A rule activates for an asset when its equations become true for one of the asset's devices, and Navixy then sends one notification. This page explains which messages a rule checks and how each kind of equation is checked. It also explains when a rule activates and deactivates, and what a change to a rule does. It's for developers who build a rule editor or a client that reads rule history. For the requests themselves, see [Creating and managing rules](guides/creating-and-managing-rules.md).

## Which messages a rule checks

A rule checks every message from the devices assigned to its covered assets. A message is one set of data that a GPS device sends, such as its position and speed at one moment. Navixy checks each message when it arrives, with that message's own values.

Navixy checks a device's messages only while the device is assigned to an asset:

* A device that isn't assigned to an asset is never checked.
* When you replace the device on an asset, the rule checks the new device without any change to the rule.
* When you move a device to another asset, the rule starts to check the device's messages for the new asset within about a minute. Until then, the rule can still check them for the former asset.
* When `assetIds` is empty, the rule covers every asset of the workspace, including the assets added later.

Rules check the messages that [IoT Logic](../iot-logic/README.md) passes on. An equation can compare a value calculated by an IoT Logic flow in the same way as a value sent by the device. See [Values calculated by IoT Logic](#values-calculated-by-iot-logic). IoT Logic skips a message older than the last message that it processed from the same device, so a rule never checks such a message. A rule also skips a message older than the last message that the rule checked from the same device.

## How equations combine

A rule has 1 to 16 equations, and Navixy checks them in order, from the first to the last. The first equation has no `connector`. Every later equation has `AND` or `OR`, which joins it to the result of all the equations before it. `AND` and `OR` have the same priority, and there are no brackets.

For example, a rule has these equations, in this order:

| Order | `connector` | Equation |
| --- | --- | --- |
| A | None | `speed` greater than 90 |
| B | `OR` | The time is within the Working hours schedule |
| C | `AND` | The position is inside the Depot geo object |

Navixy combines the equations into one chain, from left to right: `(A OR B) AND C`. The rule is true only when the asset is inside Depot and either the speed is over 90 or the time is within Working hours. To get `A OR (B AND C)`, put the speed equation last: `B AND C OR A`.

## Parameter equations

A parameter equation compares one value of the message with a threshold. Its `kind` is `PARAMETER`, and it has four fields:

* `field`: The parameter name as the device data has it, such as `speed` or `lls_level`. The name has only lowercase letters, digits, and single underscores between parts. It has at most 48 characters.
* `index`: The channel number, for a parameter that a device reports on several channels, such as several fuel level sensors. `index: 0` is a channel: it's a different parameter from one without `index`. The data has `lls_level_2` for channel 2 of `lls_level`, and the equation has `field: "lls_level"` and `index: 2`. A `field` with the channel number at the end, such as `lls_level_2` without `index`, is refused, because such a field never matches.
* `operator`: How to compare.
* `threshold`: The value to compare with, and the upper end for a range.

A `field` that no device reports is accepted and never matches. To offer only the names that your devices report, list them with the `parameterFields` query. See [List the parameters for the editor](guides/creating-and-managing-rules.md#list-the-parameters-for-the-editor).

### Values calculated by IoT Logic

An IoT Logic flow adds each value that it calculates to the message, under the name of the attribute that the flow's Initiate Attributes node writes. An equation compares this value like a parameter that the device sent:

* `field` is the attribute name. A name that ends with an underscore and digits names a channel. For example, the attribute `fuel_avg_2` is `field: "fuel_avg"` and `index: 2`.
* The value type follows the result of the expression. A number is compared with a `number` threshold, true or false with `boolean`, text with `string`, a date and time with `timestamp`, and a period with `duration`.
* A calculated value has no unit. Navixy compares it as it is, with or without `unit` in the threshold. See [Units](#units).
* When the flow writes an attribute with the same name and channel as a parameter of the device, the rule sees the calculated value.

The position values have different names in a rule and in an IoT Logic expression. A rule compares the speed as `speed`, the altitude as `altitude`, and the heading as `heading`. An expression reads the same values as `position_speed`, `position_altitude`, and `position_heading`. For example, a flow can calculate `speed_mph` from `position_speed * 0.621371`, and a rule then compares `speed_mph`.

The rule checks the calculated value only while the flow is turned on and the device is in the flow. To list the attribute names that the flows calculate for your devices, use the `deviceAttributes` query of IoT Logic. See [Find the attribute names of your devices](../iot-logic/guides/reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).

### Threshold types and operators

The threshold's `value` has exactly one of five fields: `number`, `boolean`, `string`, `timestamp`, or `duration`. The type decides the operators that the equation accepts:

| Threshold type | Format | Operators |
| --- | --- | --- |
| `number` | A decimal number written as a string, such as `"90"` or `"12.5"`, with at most 38 significant digits and the decimal point at most 38 places away from them | `EQ`, `NE`, `GT`, `GE`, `LT`, `LE`, `IN_RANGE`, `OUT_OF_RANGE` |
| `timestamp` | [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) date and time, such as `"2026-10-28T08:00:00Z"` | The same eight |
| `duration` | ISO 8601 duration in days, hours, minutes, and seconds, such as `"PT5M"` for five minutes. Years, months, and weeks are refused. | The same eight |
| `boolean` | `true` or `false` | `EQ`, `NE` |
| `string` | Text, compared exactly | `EQ`, `NE` |

A reported value of another type than the threshold makes every operator false, including `NE`. For example, a `number` threshold never matches a value that the device reports as text.

`IN_RANGE` and `OUT_OF_RANGE` need `threshold.upper`, of the same type as `value` and not less than it. Both ends belong to the range. With any other operator, `upper` is refused.

### Units

`threshold.unit` is optional and applies only to a `number`. It accepts one of these [UCUM](https://ucum.org/ucum) codes: `km/h`, `Cel`, `L`, `%`, `V`, `kPa`, and `h`. Navixy doesn't convert units:

* Without `unit`, Navixy compares the value as the device reports it. For example, `speed` is in kilometers per hour, so a threshold of `90` means 90 km/h.
* With `unit`, a reported value with the same unit, or with no unit, is compared as it is. A value with another unit makes the equation false.

### A parameter that the message doesn't have

When a message doesn't have the parameter, Navixy uses the latest value of that parameter from any device of the same asset, this device included. Navixy uses the value only when its time is at most 15 minutes before or after the message's time. Otherwise, the equation is false.

A missing value makes every operator false, including `NE` and `OUT_OF_RANGE`. A value that the device cleared also makes every operator false.

`speed`, `altitude`, and `heading` come from the message's position. Navixy reads them only from a position with a current GPS fix. For a message without one, these parameters count as missing, and Navixy uses the asset's latest values.

## Geofence equations

A geofence equation checks the position in the message against a geo object of the workspace. Its `kind` is `GEOFENCE`, with `geoObjectId` and `geofenceRelation`. The geo object's geometry must be a `Polygon` or a `MultiPolygon`. A geo object with a point or a line is refused with `UNSUPPORTED_GEOMETRY`.

| `geofenceRelation` | True for a message whose position is |
| --- | --- |
| `INSIDE` | Inside the geo object |
| `OUTSIDE` | Outside the geo object |
| `ENTRY` | Inside, while the device's previous position was outside |
| `EXIT` | Outside, while the device's previous position was inside |

`ENTRY` and `EXIT` are true only for the one message that crosses the border. A rule activated by `ENTRY` alone deactivates at the device's next message, unless another equation or another device of the asset keeps it active.

A message has a position when both coordinates are present and valid, and the GPS fix is current: not missing and not a last known position. For a message without a position, Navixy uses the device's last known relation to the geo object. `INSIDE` and `OUTSIDE` use that relation, and `ENTRY` and `EXIT` are false.

After you turn on or change a rule, Navixy doesn't know where a device is. None of the four relations is true until the device sends a message with a position. `ENTRY` and `EXIT` need one more message with a position after that, because they compare two positions.

## Schedule equations

A schedule equation checks the time of the message against a schedule of the workspace. Its `kind` is `SCHEDULE`, with `scheduleId` and `scheduleRelation`: `WITHIN` or `OUTSIDE` the schedule's times. Navixy reads the schedule in the schedule's own time zone. A schedule that is turned off makes both relations false.

## When a rule activates and deactivates

A rule is active or inactive separately for each covered asset. It activates for an asset when the equations become true for at least one device of the asset. It deactivates when the equations are true for none of the asset's devices.

Navixy records each activation and deactivation in the rule's history. Only an activation sends a notification:

* Navixy sends one notification when the rule activates for the asset.
* While the rule stays active, Navixy sends nothing, even when more messages meet the equations.
* When two devices of one asset meet the equations, the rule activates once. It stays active until neither device meets them.
* A deactivation sends nothing.

Navixy notifies about the same asset again only after the equations become false and then true again.

### What a notification contains

A notification has these values:

* The rule's `notificationText`, or its `title` when the rule has no text. The text is sent exactly as written: Navixy doesn't insert values, such as the asset name, into the text.
* The `title` of the rule.
* The asset, and the device whose message activated the rule.
* The position of that message, when the message had one.
* The time when the event happened, from the message.
* The severity, which is `INFO` for every notification.

Every user of the workspace whose roles let them read the asset receives it. See [Receiving notifications](guides/receiving-notifications.md).

### Late messages

A device can store messages while it has no connection and send them later. Navixy checks such a message and records the result like any other message. It sends a notification only when the message arrives within 15 minutes of its event time.

A late activation still activates the rule. When the next message meets the same equations, the rule is already active, so Navixy records nothing. Nobody is notified until the rule deactivates and activates again. The history marks such an activation with `mayNotify: false`. See [Late messages in the history](guides/reading-rule-history.md#late-messages-in-the-history).

## What a change to a rule does

A change to a rule takes effect after a short delay, and some changes restart the checks of messages:

* `ruleCreate` stores the rule turned off. Navixy validates the whole rule when you save it.
* Turning on a rule with `ruleUpdate` validates the whole rule again, including its references. A geo object deleted since the last save is reported then. Navixy starts checking messages for the rule about 10 seconds later. Messages in between aren't checked for this rule.
* Turning off a rule validates nothing, so you can always turn off a rule whose references are deleted.
* Every other successful write restarts the checks of messages for the rule's devices, including a rename and `isEnabled: true` on a rule that is already on. Navixy forgets each device's position relative to the geo objects, so `ENTRY` and `EXIT` need two new positions. For an asset with two devices, the rule can deactivate at the first message after the change and activate again when the other device reports. That activation sends a second notification.
* A change to a geo object or a schedule applies to the rules within about a minute. Until then, the former shape or times apply.

## Limits

These limits apply to every rule:

| Limit | Value | What happens over the limit |
| --- | --- | --- |
| Equations in one rule | 16 | `EQUATIONS_PER_RULE_EXCEEDED` |
| Assets in `assetIds` | 25,000 | `ASSETS_PER_RULE_EXCEEDED` |
| `title` | 255 characters | `VALIDATION_ERROR` |
| `notificationText` | 1,000 characters | `VALIDATION_ERROR` |
| IDs in one list of the history filter | 100 | `VALIDATION_ERROR` |
| History kept | 90 days, by event time | Older events are deleted |
| From turning on a rule to its first check | About 10 seconds | Messages in between aren't checked |
| Latest values used for a missing parameter | 15 minutes before or after the message | The equation is false |
| A late message still notifies | Within 15 minutes of its event time | The activation is recorded without a notification |

See [Rule errors](rule-errors.md) to learn what each limit error means and how to fix it.

## See also

* [Creating and managing rules](guides/creating-and-managing-rules.md): Create a rule, turn it on, change it, and delete it.
* [Reading rule history](guides/reading-rule-history.md): Find out why a rule sent a notification, or why it didn't.
* [How rules work](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/rules-and-notifications/how-rules-work) in Location Intelligence: Learn how users see the same rules in the rule editor.
