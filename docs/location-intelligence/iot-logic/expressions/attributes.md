---
description: >-
  Which attribute names IoT Logic accepts, how a sensor channel is written,
  and what happens when a name doesn't match.
tags:
  - fundamentals
  - beta
---

# Attribute names

Every value in a message has a name. You use the name to read the value in a formula, and you choose a name when an **Initiate Attribute** node produces a new value.

Names are strict, and a name that is nearly right behaves as if the value weren't there at all.

## Characters allowed in an attribute name

An attribute name uses lowercase Latin letters and digits, separated by underscores.

| Legal | Not legal | Why |
| --- | --- | --- |
| `fuel_level` | `Fuel_Level` | Capitals |
| `temp_fahrenheit` | `temp fahrenheit` | Space |
| `engine_hours` | `engine-hours` | Hyphen |
| `bms_battery_soc` | `température` | Not Latin |

A name is at most 48 characters.

The same rule applies to the names that you write in a formula and the names that you give to calculated attributes. There is no separate, looser rule for reading.

## Sensor channels

A device with more than one sensor of the same type reports each sensor on its own channel. The channel is written after the name:

```
lls_level_1
```

Channel numbering starts at 0, so a device with two fuel sensors reports `lls_level_0` and `lls_level_1`.

{% hint style="warning" %}
Avoid ending a base attribute name with a digit. A digit at the end of a name is read as a channel number, so write `sensor_two` rather than `sensor_2`, or put the distinguishing part earlier in the name.
{% endhint %}

## When a name doesn't match

Nothing warns you. When a formula reads a name that isn't in the message, the formula returns nothing, and the result depends on the node:

* In an **Initiate Attribute** node, the attribute isn't produced for that message.
* In an **IF/THEN Logic** node, the operator decides where the message goes. Most operators send the message to **else**, and `!=` and `!~` send it to **then**. See [Missing values](missing-values.md).

The flow keeps running and the message still reaches the Navixy platform. There is no error to find, which is what makes a mistyped name hard to spot. See [Missing values](missing-values.md), which is the same silence from the other direction.

{% hint style="info" %}
Use the attribute button in an expression field to insert the name of an attribute that the flow calculates. It lists only those. For the device's own readings, copy the names from [Flow monitoring](../monitoring.md).
{% endhint %}

## Names you choose

When an **Initiate Attribute** node produces a value, you name it. These rules apply on top of the spelling rule.

**The name must be unique for the devices it reaches.** The flow doesn't save when two of its nodes that the same message passes through produce the same name. The **then** and **else** branches of one **IF/THEN Logic** node can each produce it, because a message follows only one of them. It also doesn't save when another flow produces the same name on a device that this flow also covers. Two flows that cover separate fleets may use the same name, because no device receives the value twice.

**A name the devices report stops the save.** A calculated attribute and a reported one share a namespace. The save fails when a device that the flow covers has reported the name, even once. If a device starts reporting the name after the flow is saved, the calculated value replaces that device's reading, and the next save of the flow fails.

**Twelve names are reserved.** A flow can't produce an attribute called `position_latitude` or any of the other eleven position names, listed in [Expressions](README.md).

**An IF/THEN Logic node's Expression name is one of these names.** When set, it produces an attribute like any other, so it has to be unique for the devices it reaches too.

**Name the value, not the node.** `fuel_level_percent` says what the value is. `attribute_1` and `logic_node_output` don't. The name appears in monitoring and in every formula that reads the value later.
