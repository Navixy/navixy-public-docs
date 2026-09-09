---
description: >-
  Understand why an IoT Logic formula or condition produces no value when a
  value it needs is missing, and which comparison patterns prevent it.
---

# Missing values in expressions

Formulas and conditions in IoT Logic work on the values that a GPS device sends. When a value that a formula needs is missing, most operators can't produce a result, and the formula produces nothing at all: not `false`, and not zero. This page explains when a value goes missing, what the failure looks like, and how to write formulas and conditions that keep working. It is for anyone who configures an IoT Logic flow, and it assumes no programming background.

The page applies to both places where you write an expression: the **Formula** field of an [Initiate Attribute](initiate-attribute-node/) node, and the **Condition expression (JEXL)** field of an [IF/THEN Logic](logic-node/) node.

## When an expression can't produce a value

An empty value stops the whole expression. That applies on either side of a calculation, a size comparison, a text prefix test, or a logical `!`, `&&`, or `||`. In a formula and in Data Stream Analyzer, an empty value appears as `null`.

Reading an attribute that a GPS device has never sent isn't an error by itself. What stops is the operator that then has to work with the empty value. One empty value stops the entire expression, including the parts that had values. A condition that tests four geofences stops at the first one that can't answer, and the other three are never tested. The exception is a term earlier in the expression that has already settled the result.

## What you see when an expression produces nothing

Nothing marks the failure. The result depends on which node holds the expression:

| Where the expression runs | What happens for that data packet |
| ------------------------- | --------------------------------- |
| **Formula** field of an **Initiate Attribute** node | The attribute has no value for that packet, and Data Stream Analyzer shows it as `null`. |
| **Condition expression (JEXL)** field of an **IF/THEN Logic** node | The data leaves through the ELSE connection, and the node stores its own attribute as an empty value, not as `false`. |

{% hint style="warning" %}
Nothing reports the failure. No error appears in the flow, no counter changes, and every other attribute is still calculated normally. The only sign is the missing value itself, so a flow can look healthy while one attribute produces no value for weeks.

Saving a flow doesn't prove that a formula works either. The check that runs when you save confirms that the expression is written correctly. It doesn't run the expression against the data of your GPS device, so a formula that produces nothing on every packet still saves without a warning.
{% endhint %}

## How each operator handles an empty value

Four operators always keep working on an empty value: `==`, `!=`, `=~`, and `!~`. The operators `&&` and `||` keep working only when the side read first settles the answer. Every other operator stops.

This table covers an attribute that you reference by name, which is the form that [autofill](initiate-attribute-node/managing-attributes.md#autofill-attribute-names) inserts. Four operators behave differently when the value comes from `value()` instead. See [Reading by name or with value()](#reading-by-name-or-with-value).

| Operator | With an empty value on either side |
| -------- | ---------------------------------- |
| `==`, `!=` (equal to, not equal to) | Gives a real yes or no. `null == null` is `true`, and `null != null` is `false`. |
| `=~`, `!~` (is in a list, or matches a text pattern) | Gives a real yes or no. An empty value never matches, so `=~` gives `false` and `!~` gives `true`. |
| `??` (use a fallback) | Gives the value on the right. It substitutes for an empty value only. |
| `?:` (use a fallback) | Gives the value on the right. It also substitutes for `false`, for `0`, and for empty text. |
| `a ? b : c` (choose between two values) | Takes the `c` branch, the same as it does for `false`. |
| `=^`, `!^`, `=$`, `!$` (starts with, ends with) | The expression produces nothing. |
| `<`, `<=`, `>`, `>=` (size comparisons) | The expression produces nothing. |
| `+`, `-`, `*`, `/`, `%` (arithmetic) | The expression produces nothing. |
| `&`, `\|`, `^` (bit operations) | The expression produces nothing. |
| `!` (not) | The expression produces nothing. |
| `&&`, `\|\|` (and, or) | The expression produces nothing, unless the side read first settles the answer. |

The six text operators split in two. `=~` and `!~` survive an empty value, but `=^`, `!^`, `=$`, and `!$` don't when the attribute is referenced by name. A condition such as `hardware_key !^ 'AB'` looks like a safe way to exclude a prefix, but it produces nothing on every packet with no `hardware_key`.

The `math:` functions produce nothing when they receive an empty value, so `math:round(engine_temp)` produces nothing when `engine_temp` is missing. The `util:` functions are more tolerant and return an empty value instead, so `util:join` and `util:joinNonNull` still produce a result when one of their arguments is missing. Joining text with `+` is not tolerant: `'Zone: ' + zone_label` produces nothing.

## Logical operators with an empty value

Order matters for both `&&` and `||`. Each operator reads the left side first and stops there when the left side already settles the result. A `false` left side settles `&&`, and a `true` left side settles `||`.

| Expression | Result |
| ---------- | ------ |
| `false && (empty)` | `false`. A false left side settles AND, so the right side is never read. |
| `true && (empty)` | Produces nothing. |
| `(empty) && anything` | Produces nothing. The left side is read first. |
| `true \|\| (empty)` | `true`. A true left side settles OR, so the right side is never read. |
| `false \|\| (empty)` | Produces nothing. |
| `(empty) \|\| anything` | Produces nothing. The left side is read first. |

Putting the term that might be empty last helps only when the left side settles the result on that packet. You can rarely guarantee that outcome, so compare the value explicitly instead.

## Reading by name or with value()

Four operators give different results for the same missing value, depending on how the formula reads it. The operators are `=^`, `!^`, `=$` and `!$`, the ones that test the start or the end of a text value.

| Formula | Result when `driver_id` was never sent |
| ------- | -------------------------------------- |
| `driver_id !^ 'AB'` | Produces nothing |
| `value('driver_id', 0, 'all') !^ 'AB'` | `true` |

Referencing the attribute by name is the form that [autofill](initiate-attribute-node/managing-attributes.md#autofill-attribute-names) inserts, so a condition built with the attribute picker takes the first path. Write the comparison with `value()` when you need `!^` or `!$` to report `true` on a packet that doesn't carry the attribute.

Every other operator behaves the same in both forms. A size comparison, arithmetic, and `!` all produce nothing either way, and `==`, `!=`, `=~` and `!~` all give a real yes or no either way.

The same split applies to the two fallback forms. `??` and `? :` can contain a failed calculation when the value comes from `value()`, and can't when it comes from a name:

| Formula | Result when `odometer` was never sent |
| ------- | ------------------------------------- |
| `(odometer + 1) ?? 0` | Produces nothing |
| `(value('odometer', 0, 'all') + 1) ?? 0` | `0` |

A plain `??` on a name still works, because no calculation happens before it. Only a calculation wrapped inside `??` or `? :` behaves differently.

## Compare a value explicitly

To make a value that might be empty safe in a condition, compare it with `== true` or `== false` instead of using it on its own. Both `==` and `!=` give a real yes or no for an empty value, so one term that has no data no longer stops the whole condition.

This condition stops on any packet where one of the two attributes is missing:

```jexl
door_open || hood_open
```

This condition keeps working, and reports yes when either door is open:

```jexl
door_open == true || hood_open == true
```

The geofence functions behave the same way, because they return an empty value when a packet carries no position. This condition stops as soon as one function can't answer:

```jexl
leaveGeofence(51577 /* Austin Warehouse */) || leaveGeofence(85269 /* Construction site 1 */)
```

This condition tests both geofences on every packet:

```jexl
leaveGeofence(51577 /* Austin Warehouse */) == true || leaveGeofence(85269 /* Construction site 1 */) == true
```

Choose the comparison that matches what a packet with no data should count as:

* `== true` means "known to be yes". An empty value gives `false`, so a packet with no data doesn't match.
* `== false` means "known to be no". An empty value gives `false`, so a packet with no data doesn't match.
* `!= true` means "not known to be yes". An empty value gives `true`, so a packet with no data does match.

{% hint style="warning" %}
Don't use `!` on a value that might be empty. `!inGeofence(35229)` produces nothing when the position is unknown, so it can't detect a device outside the geofence. Write `inGeofence(35229) == false` for "known to be outside", or `inGeofence(35229) != true` for "not known to be inside".
{% endhint %}

## Where empty values come from

An attribute in a formula resolves to an empty value in these cases:

* The attribute has never been sent for that GPS device.
* The current packet doesn't carry the attribute, even though an earlier packet did.
* The history position that you requested with `value()` doesn't exist yet, because the device has sent fewer packets than the position requires.
* The device sent nothing for more than 30 days, so Navixy dropped its stored history. See [Full syntax](initiate-attribute-node/managing-attributes.md#full-syntax).
* `inGeofence()`, `enterGeofence()`, or `leaveGeofence()` can't answer, because the packet carries no coordinates or the geofence no longer exists in the account. See [When a geofence function gives no answer](geofence-functions.md#when-a-geofence-function-gives-no-answer).
* `geofenceName()` has no name to return. See [What you get back](geofence-functions.md#what-you-get-back).

All of these causes look the same in a formula. A formula that must keep working has to handle the empty value rather than the cause.

## Formulas that must always produce a value

Guard the calculation with `!= null`, and put the guard first. The guard uses `!=`, which gives a real yes or no for an empty value, so the guard itself never stops:

```jexl
value('odometer', 0, 'all') != null && value('odometer', 0, 'all') > 100
```

The order is what makes the guard work. If you reverse the terms, the comparison runs before the guard, and the whole condition produces nothing.

To substitute a fallback value instead, use `??`. It returns the right side only when the left side is empty:

```jexl
can_speed ?? 0
```

`??` substitutes for a missing value, not for a failed calculation. `(can_speed * 1.1) ?? 0` produces nothing when `can_speed` is missing, because the multiplication stops before `??` is reached. Put `??` around the attribute rather than around the calculation:

```jexl
(can_speed ?? 0) * 1.1
```

{% hint style="warning" %}
`?:` and `??` look similar and behave differently. `??` substitutes the right side only for an empty value. `?:` substitutes it for an empty value and also for `false`, for `0`, and for empty text. The formula `ignition ?: true` therefore reports `true` when the ignition is really off. Use `??` when you mean "if the value is missing".
{% endhint %}

You can also gate the calculation from outside the formula. Put an **IF/THEN Logic** node before the **Initiate Attribute** node, and test the attributes for presence in its condition. Connect the THEN path to the calculating node, so it receives only packets that carry the values that it needs.

Connect the ELSE path to an **Output Endpoint** node as well. A branch that ends without one drops every packet that reaches it, so the packets that failed the presence test never leave the flow.

When no value is better than a wrong one, an attribute with no value is the correct outcome. Add a fallback only when a system further down the flow needs a value on every packet.

## Check whether an expression produces nothing

Work from what you can see in [Data Stream Analyzer](../data-stream-analyzer.md):

1. Select the flow, then the GPS device and the calculated attribute.
2. Turn off the **Exclude null** toggle. A failed calculation appears as a `null` value. The toggle hides those values when it is on.
3. Raise the depth if the packet that you are looking for is older than the values on screen.
4. Compare the attribute with the attributes that its formula reads. A packet where the formula inputs have values and the calculated attribute is `null` points at the formula.
5. Check every operator in the formula against [How each operator handles an empty value](#how-each-operator-handles-an-empty-value).
6. Rewrite each part that might be empty. Use `== true`, `== false`, or a `??` fallback.

{% hint style="warning" %}
When **Exclude null** is on, the last value that the formula produced stays on screen with its own older timestamp. The reading looks current, but it belongs to an earlier packet. Switch the toggle off before you judge whether IoT Logic still calculates an attribute.
{% endhint %}

## Frequently asked questions

#### Is an empty value the same as `false`?

No. A condition that is genuinely `false` stores `false`, and a condition that couldn't be evaluated stores an empty value. Both send the data through the ELSE connection of an **IF/THEN Logic** node, so the branch alone doesn't tell you which happened. A later expression that tests the attribute with `my_flag == false` matches only the first case.

#### Why did one GPS device stop matching a condition that still works for the others?

Devices of different models don't all send the same attributes, and one device can stop reporting an attribute that the others still send. A condition that reads that attribute without a comparison then produces nothing for that device only. Compare each term explicitly with `== true` or `== false`.

## See also

* [IF/THEN Logic expressions and syntax](logic-node/logic-node-expressions-and-syntax.md#missing-values-and-null-routing) for which branch a Logic node takes and what it stores.
* [Geofence functions](geofence-functions.md) for the cases where a geofence function can't answer.
* [Managing attributes](initiate-attribute-node/managing-attributes.md#expression-language) for the `value()` function and the two syntax options.
* [Calculation examples](initiate-attribute-node/calculation-examples.md) for formula examples.
