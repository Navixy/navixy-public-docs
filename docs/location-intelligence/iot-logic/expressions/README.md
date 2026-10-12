---
description: >-
  The IoT Logic expression language: how to read a value, what operators and
  functions are available, and what an expression can't do.
tags:
  - fundamentals
  - beta
---

# Expressions

Everywhere IoT Logic calculates something or asks a question, you write it as a formula in a field. An **Initiate Attribute** node uses a formula to produce a value. An **IF/THEN Logic** node uses a formula to decide a direction.

Both nodes use the same language, and this section covers that language. The interface calls the field **Expression**, or **Condition expression** in an **IF/THEN Logic** node. These pages call what you write in it a formula.

## Reading a value

Write the name of an attribute to get the value that the device just sent:

```
speed
```

```
temperature * 1.8 + 32
```

The attribute name alone is the short form, and it covers most formulas. The long form does the same and takes arguments:

```
value('speed')
```

The long form is what you need to read a value from an earlier message. See [Earlier values](history.md). The two forms differ only for a value that is missing from the message, and only with a few operators. See [Missing values](missing-values.md).

{% hint style="info" %}
A name that isn't in the message returns nothing. It doesn't fall back to the last value that the device sent. See [Missing values](missing-values.md) to learn what an absent value does to a condition, and [Earlier values](history.md) to learn how to get an earlier reading on purpose.
{% endhint %}

## Names that are always available

Two names describe the message itself:

| Name | What it gives you |
| --- | --- |
| `device_id` | The identifier of the device the message came from |
| `event_time` | When the device recorded the message, in milliseconds |

Twelve more names contain the position of the device, when the message has a position. These names behave like any other attribute, so you can read an earlier value by index:

`position_latitude`, `position_longitude`, `position_altitude`, `position_speed`, `position_heading`, `position_satellites`, `position_hdop`, `position_pdop`, `position_vdop`, `position_fix_type`, `position_accuracy`, `position_source`

`position_speed` is the speed from the position fix. It isn't always the same value that the vehicle's own bus reports as `speed`.

{% hint style="info" %}
These 12 names are reserved. A flow can't produce an attribute with one of these names. If a node tries, the flow doesn't save.
{% endhint %}

## Operators

The arithmetic operators are `+`, `-`, `*`, and `/`.

Dividing one whole number by another drops the fraction: `7 / 2` is `3`. When either number has a decimal point, the result keeps the fraction: `7 / 2.0` is `3.5`. A device often sends whole numbers, so write a constant with a decimal point, such as `battery_mv / 1000.0`, whenever the fraction matters.

For the comparison and logical operators used in conditions, see [IF/THEN Logic](../nodes/logic.md).

## Functions

| Function | What it does |
| --- | --- |
| `value(name)` | The value of an attribute |
| `value(name, index)` | The value from an earlier message, index 0 to 11 |
| `value(name, index, type)` | The value from an earlier message, counted a particular way |
| `genTime(name)` | When the device recorded that value |
| `srvTime(name)` | When the Navixy platform received it |
| `dtFormat(timestamp)` | A timestamp in milliseconds written out as text in UTC, in ISO 8601 format, such as `2026-10-01T12:00:00Z`. It takes no format pattern |

`genTime` and `srvTime` take the same arguments as `value`.

### Math

Standard math functions are available with a `math:` prefix:

```
math:round(battery_voltage)
```

```
math:sqrt(pressure)
```

```
math:abs(temperature_delta)
```

### Utilities

Utility functions use a `util:` prefix. `util:clear()` produces an empty value. A formula uses `util:clear()` to remove an attribute on purpose, instead of giving the attribute a number.

## What an expression can't do

A formula works with the current message and the earlier readings of the same device.

* **Time comes from the message.** Use `event_time` to get the time when the device recorded the message. There is no `now()`.
* **It can't loop, and it can't define a function.** Every formula is a single calculation.
* **It can't reach outside the message.** A formula can't read the data of another device, call another system, or read a database.

{% hint style="warning" %}
Before you write a condition on a value, check in [Flow monitoring](../monitoring.md) which data type the device sends for that value. The formula saves even when a value arrives as text and you expected a number, so rule out this case early.
{% endhint %}

## Limits

| Limit | Value |
| --- | --- |
| Expression in an Initiate Attribute node | 960 characters |
| Condition in an IF/THEN Logic node | 480 characters |

A formula that is close to its limit usually does the work of two attributes. Calculate the intermediate value in its own **Initiate Attribute** node, and read that value in the next node.

## The full language reference

These pages explain how to use expressions in the nodes. The language itself is documented separately, in [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/overview).

* For the complete syntax, every operator, every `math:` and `util:` function, and the exact null rules, see the [expression syntax reference](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/expression-syntax-reference). The reference also describes bit-level decoding, HEX conversion, BCD, padding, and joining, and this section doesn't repeat them.
* When a formula is rejected and the error message doesn't tell you enough, see the [formula error reference](https://app.gitbook.com/s/8Ti8OItwJGZAFzK8jsG6/navixy-iot-logic-expression-language/formula-errors).

## In this section

* [Attribute names](attributes.md): Learn which names are legal and what happens when a name is wrong.
* [Earlier values](history.md): Learn how to read what a device sent before this message.
* [Missing values](missing-values.md): Learn what happens when the value isn't in the message, and which operators still return a result.
* [Formula examples](examples.md): See worked formulas for common cases.
