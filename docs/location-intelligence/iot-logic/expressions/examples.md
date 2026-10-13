---
description: >-
  Worked IoT Logic formulas for unit conversion, thresholds, change over
  time, and values calculated only for the messages they apply to.
tags:
  - reference
  - beta
---

# Formula examples

This page gives formulas for the most common cases. Each example shows which node the formula goes in: an **Initiate Attribute** node produces a value, and an **IF/THEN Logic** node asks a question.

Where a formula divides, one of the numbers has a decimal point, such as `100.0`. Dividing one whole number by another drops the fraction. See [Operators](README.md#operators).

## Converting units

**Celsius to Fahrenheit**, in an **Initiate Attribute** node:

```
temperature * 1.8 + 32
```

**A raw sensor count to liters**, with the coefficient from the sensor's calibration. Here, one count is 0.1 liters:

```
fuel_raw * 0.1
```

**A reading to a percentage of its full range**, here a 120-liter tank, rounded to a whole percent:

```
math:round(fuel_level * 100.0 / 120)
```

**Millivolts to volts, to one decimal place:**

```
math:round(battery_mv / 100.0) / 10.0
```

## Asking a question about the current message

**Over the speed limit**, in an **IF/THEN Logic** node:

```
speed > 90
```

**Both conditions at once:**

```
temperature > 25 && humidity > 80
```

**Either condition:**

```
temperature < -5 || temperature > 30
```

**One of a set of values:**

```
lock_state =~ ['unlocked', 'tampered']
```

**A value that the device didn't send:**

```
fuel_level == null
```

## Comparing against earlier readings

**The change since the last reading**, in an **Initiate Attribute** node:

```
fuel_level - value('fuel_level', 1, 'valid')
```

**A sudden drop**, more than 40 liters between two readings, in an **IF/THEN Logic** node:

```
value('fuel_level', 1, 'valid') - fuel_level > 40
```

**The average of the last three readings:**

```
(value('temperature', 0) + value('temperature', 1, 'valid') + value('temperature', 2, 'valid')) / 3.0
```

**Whether the device has moved since the previous message:**

```
value('position_latitude', 0) != value('position_latitude', 1, 'all')
```

## Working with time

**Milliseconds since the previous reading of this attribute:**

```
event_time - genTime('fuel_level', 1, 'valid')
```

**The time of the message as readable text:**

```
dtFormat(event_time)
```

**The local time of day, in seconds since midnight**, in an **Initiate Attribute** node. This example is for UTC+2, an offset of 7,200 seconds:

```
(event_time / 1000 + 7200) % 86400
```

`event_time` is in milliseconds and counted in UTC. Dividing by 1000 gives whole seconds, and `% 86400` keeps the seconds since midnight. Store the result as an attribute, such as `local_seconds`. An **IF/THEN Logic** node can then compare it, for example `local_seconds >= 64800` for 18:00 or later.

A flow runs only when a message arrives, so the condition applies from the first message after 18:00, not at 18:00 exactly. The offset is fixed: change it by hand when daylight saving time starts or ends.

## Combining values

**Total load across two sensors:**

```
axle_load_0 + axle_load_1
```

**Fuel used per hour, guarding against division by zero:**

```
engine_hours > 0 ? fuel_used * 1.0 / engine_hours : 0
```

## Removing a value

`util:clear()` produces an empty value, so a formula can remove an attribute rather than give it a number:

```
speed > 200 ? util:clear() : speed
```

The formula treats a reading above 200 as a sensor fault and clears that reading. The formula passes every other reading through. Name the result something like `speed_checked`: a flow can't produce an attribute the devices themselves report.

## Chaining attributes

An attribute calculated in one **Initiate Attribute** node can be read in the next **Initiate Attribute** node. Use this approach rather than one long formula:

First node, `fuel_percent`:

```
math:round(fuel_level * 100.0 / 120)
```

Second node, `fuel_percent_change`:

```
fuel_percent - value('fuel_percent', 1, 'valid')
```

Chaining also works inside one node. A node calculates its attributes in the order that they are listed, and each attribute can read the attributes above it. Separate nodes make the dependency visible on the canvas.

## Calculating a value only for some messages

Some values make sense only for some messages. In a mixed fleet, only some trucks carry a fuel probe. An **IF/THEN Logic** node sends the messages that have a probe reading to an **Initiate Attribute** node, and sends the other messages directly to the **Output Endpoint** node.

The flow has four nodes:

1. A **Data Source** node with the whole fleet
2. An **IF/THEN Logic** node with the condition `fuel_raw != null` and the **Expression name** `has_fuel_probe`
3. On the then branch, an **Initiate Attribute** node with the three attributes below
4. One **Output Endpoint** node, which both branches connect to

The **Initiate Attribute** node calculates its attributes in order, so each attribute can read the attributes above it.

**Liters**, as `fuel_litres`, with the coefficient from the probe's calibration:

```
fuel_raw * 0.1
```

**A percentage of a 120-liter tank**, as `fuel_percent`:

```
math:round(fuel_litres * 100.0 / 120)
```

**The change since the last message**, as `fuel_change`:

```
fuel_litres - value('fuel_litres', 1, 'valid')
```

`'valid'` reads the last `fuel_litres` that the flow calculated, even when the truck sent messages without a probe reading in between. `fuel_change` produces nothing until an earlier value exists. After that, a negative number means that fuel was used or drained. See [Earlier values](history.md).

Trucks without a probe get none of the three attributes, and no formula has to guard against a missing reading. Every message has `has_fuel_probe`, so Data Stream Analyzer and Tracking show which trucks reported a probe reading.

A single value can also guard itself, with a formula such as `fuel_raw != null ? fuel_raw * 0.1 : null`. An **IF/THEN Logic** node is the clearer choice when several values depend on the same condition.

The two branches can each produce an attribute with the same name, because a message follows only one of them. Two nodes on the same branch can't: a flow in which they both produce `fuel_litres` doesn't save.

## What to check when a formula produces nothing

Work through the checks in [A calculated attribute doesn't appear](../troubleshooting/README.md#a-calculated-attribute-doesnt-appear), in order. The most common cause is an attribute name that doesn't match what the device sends.
