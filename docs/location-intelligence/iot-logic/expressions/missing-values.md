---
description: >-
  What an IoT Logic formula does when the value it reads is not in the
  message, which operators still give an answer, and how to guard against it.
tags:
  - fundamentals
  - beta
---

# Missing values

Each message contains the values that the device had at the time. A sensor reports on its own schedule, a position fix depends on satellite visibility, and a value that you expect can be absent from a message.

A formula that reads an absent value produces nothing, and the flow continues. There is no error and no warning anywhere, so read this page before you write a condition.

## What a missing value does in each node

A missing value behaves differently depending on which node reads it.

**In an Initiate Attribute node**, the attribute isn't produced for that message. The next node receives the message without that attribute, exactly as if the formula weren't there. The formulas of other attributes in the same node still run, each on its own.

**In an IF/THEN Logic node**, anything that isn't a real true or false sends the message down the **else** branch. This rule covers a formula that failed and a formula that returned nothing.

In neither case does IoT Logic report the missing value. There is no error in the interface and no mark on the node. You find out when you look at [Flow monitoring](../monitoring.md) and see that the attribute is absent.

## How to guard a condition

Test that the value exists before you use it, and put the test first:

```
fuel_level != null && fuel_level < 10
```

`&&` stops at the first false, so when `fuel_level` is missing, the comparison never runs and the condition is false. The message goes to **else**, as intended.

The order matters, and not only for tidiness. `&&` reads its operands from left to right, and a null that it reads stops the evaluation. When you write the test the other way round, the comparison runs first and stops the formula, so the result is nothing rather than `false`:

```
fuel_level < 10 && fuel_level != null
```

## Which operators still give an answer

Some operators treat a missing value as a value and return a result as usual. The other operators stop, and the formula produces nothing.

| What you write | When the value is missing | The message goes to |
| --- | --- | --- |
| `x == 5` | false | else |
| `x != 5` | **true** | **then** |
| `x =~ ['a', 'b']` | false | else |
| `x !~ ['a', 'b']` | **true** | **then** |
| `x == null` | true | then |
| `x != null` | false | else |
| `x > 5`, `x < 5`, `x >= 5`, `x <= 5` | stops | else |
| `x + 1`, `x * 2`, any arithmetic | stops | (no attribute produced) |
| `!x` | stops | else |
| `math:round(x)` | stops | (no attribute produced) |
| `util:hex(x)` | `null` | else, or no attribute |
| `x =^ 'AB'`, `x !^`, `x =$`, `x !$` | stops | else |
| `value('x', 0) =^ 'AB'` | false | else |
| `value('x', 0) !^ 'AB'` | **true** | **then** |

"Stops" means the whole formula stops, not just that term. `(temperature + 10) > 30` produces nothing when `temperature` is missing, and nothing isn't `false`.

The last three rows differ by how you write the value, not by what the value is. The starts-with and ends-with operators return one result for a bare attribute name and a different result for `value()`. Write the bare name for these four operators, so that a missing value stops the formula instead of returning a result.

{% hint style="danger" %}
**`!=` returns true when the value is missing.** `speed != 60` is true for a device that reports no speed at all, so every message from that device takes the **then** branch. `!~` behaves the same way, and so do `!^` and `!$` in the `value()` form, such as `value('x', 0) !^ 'AB'`.
{% endhint %}

## How to test a yes/no value

A yes/no attribute, such as `ignition`, can be missing too. Compare it with `true` or `false` instead of using it on its own. A comparison always gives an answer:

| What you write | What it means | When the value is missing |
| --- | --- | --- |
| `ignition == true` | The ignition is known to be on | false |
| `ignition == false` | The ignition is known to be off | false |
| `ignition != true` | The ignition isn't known to be on | true |

`!ignition` stops the formula when the value is missing, and so do `&&` and `||` when they reach the missing value. In an **IF/THEN Logic** node, a term that a device never sends therefore sends every message from that device down the **else** branch.

## How to use the last known value instead

Often the condition means the last known reading. A sensor that reports every tenth message still has a last known reading, and you can ask for that reading.

Read the value by index rather than by name:

```
value('fuel_level', 1, 'valid') < 10
```

`'valid'` counts the attribute's own values and skips the messages that didn't contain the attribute, so index 1 is the last reading that the device sent. See [Earlier values](history.md).

{% hint style="info" %}
Index 0 is the current message, and it never falls back to an earlier value. `fuel_level` and `value('fuel_level', 0)` both return nothing when this message doesn't contain the attribute. Only an index of 1 or more gets an earlier value.
{% endhint %}

## How to give a value a default

Put `??` on the attribute name, then calculate:

```
(speed ?? 0) * 1.1
```

When `speed` is missing, the formula uses `0` and still produces a value.

`??` replaces only a missing value. `?:` also replaces `false`, `0`, and empty text, so `ignition ?: true` gives `true` when the ignition is off. Use `??` for numbers and for yes/no values.

## Producing nothing on purpose

`util:clear()` gives an attribute an empty value, which reads back as nothing:

```
speed > 200 ? util:clear() : speed
```

The formula treats a reading above 200 as a sensor fault and clears that reading. The formula passes every other reading through.

## What to check when a condition behaves oddly

1. **Does the device send that attribute on every message?** Look in [Flow monitoring](../monitoring.md). An attribute that appears on some messages and not others is the usual cause.
2. **Is the operator one that returns a result anyway?** `!=` and `!~` return true for a missing value, and so do `!^` and `!$` in their `value()` form. These four operators send a message with the missing value to **then**, not **else**.
3. **Is the guard in the right order?** The `!= null` test has to come first.
4. **Is the name spelled the way the device spells it?** IoT Logic refuses a name that breaks the spelling rules when you save the flow. IoT Logic accepts a name that is spelled legally but that no device sends, and that name returns nothing on every message. See [Attribute names](attributes.md).
