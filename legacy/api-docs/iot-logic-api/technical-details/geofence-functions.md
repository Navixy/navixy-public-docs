---
description: >-
  Test one geofence by ID inside IoT Logic formulas and conditions with
  inGeofence(), enterGeofence(), and leaveGeofence(), including their
  parameters, return values, and the cases that return null.
---

# Geofence functions

Three functions test one geofence that you name by its numeric ID. `inGeofence()` answers whether the device is inside it. `enterGeofence()` and `leaveGeofence()` answer whether the device has just crossed into it or out of it. Each returns a boolean, and each returns `null` when it can't answer.

The three functions are available in two node fields:

* `data.condition` of a `logic` node, where the result decides the branch.
* `data.items[].value` of an `initiate_attributes` node, where the result becomes a boolean calculated attribute.

Unlike the [geofence name function](geofence-name.md), these three need no account feature and are available on every account. Use `geofenceName()` instead when a flow needs to know which geofence a device is in without naming one in advance.

## Syntax

| Form | Position used |
| --- | --- |
| `inGeofence(id)` | Current message |
| `inGeofence(id, index)` | Message at `index`, including messages with no position |
| `inGeofence(id, validation)` | Current message, gap handling per `validation` |
| `inGeofence(id, index, validation)` | Message at `index`, gap handling per `validation` |
| `enterGeofence(id)` | Current position, compared with the previous one |
| `leaveGeofence(id)` | Current position, compared with the previous one |

`enterGeofence()` and `leaveGeofence()` take the geofence ID and nothing else. Both compare the current position with the previous one, so there is no index to choose. A flow that passes a second argument to either function is rejected at save time, and the message names the function:

```text
The formula is invalid: [1:${column} unsolvable function/method '${function}(arg, arg)']
```

`${column}` is the position of the call in the formula, and `${function}` is the function that was called with too many arguments. A three-argument call reports `(arg, arg, arg)`.

## Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `id` | Integer | Required | Numeric ID of a geofence in the account that owns the GPS device |
| `index` | Integer, 0 to 11 | `0` | Which stored message to read the position from. `0` is the current message, `1` the previous one, `11` the oldest retained one |
| `validation` | `'all'` or `'valid'` | `'all'` | How to count stored messages with no position. `'all'` counts them, so `index` addresses an exact history slot. `'valid'` skips them, so `index` addresses the nth message with a position |

`index` and `validation` behave as they do in [`value()`](nodes.md#historical-data-access-index).

The geofence ID is checked when you save the flow. An ID that names no geofence, or one that the account can't read, fails the save and nothing is stored:

```text
Node "${title}" (#${node_id}) contains nonexistent or inaccessible geozones
```

`${title}` is the `data.title` of the node, and `${node_id}` its `id`. Both are substituted with actual values in the response.

A geofence deleted after the flow was saved is not rejected again. The call returns `null` from then on, and the flow keeps running.

## Return value

Each function returns `true` or `false`. It returns `null` in the following cases.

| Cause | Applies to | Detail |
| --- | --- | --- |
| The message includes no position | All three | `latitude` or `longitude` is absent at the requested `index`, or the pair is outside the valid coordinate range. A position of poor quality is still tested |
| The geofence became inaccessible after the flow was saved | All three | Deleted, or moved out of the reach of the account. A geofence that is already inaccessible at save time fails the save instead |
| `index` is above 11 | `inGeofence()` | The flow saves without an error, and the attribute stays empty for every message |

{% hint style="warning" %}
`null` is not `false`. An operator that reads the `null` stops the whole formula: the attribute has no value for that message, a `logic` node takes the `else` path, and nothing reports it, at save time or at runtime. See [When a saved formula fails on a message](../Technologies/navixy-iot-logic-expression-language/formula-errors.md#when-a-saved-formula-fails-on-a-message).
{% endhint %}

{% hint style="warning" %}
Only `value()` rejects an `index` above 11 at save time. `inGeofence()`, `geofenceName()`, `genTime()`, and `srvTime()` accept any index and save without an error, and the attribute then stays empty for every message. Keep `index` within 0 to 11. See [Invalid value() arguments](../Technologies/navixy-iot-logic-expression-language/formula-errors.md#invalid-value-arguments).
{% endhint %}

## Compare the result explicitly

Write `inGeofence(123) == true` rather than `inGeofence(123)` whenever the call is an operand of `&&` or `||`. The equality operators accept a `null` operand and return a real `true` or `false`, so one call that can't answer no longer stops the whole condition. Replace `!inGeofence(123)` with `inGeofence(123) == false` rather than negating a comparison.

| Instead of | Write |
| --- | --- |
| `inGeofence(101) \|\| inGeofence(102)` | `inGeofence(101) == true \|\| inGeofence(102) == true` |
| `leaveGeofence(101) \|\| leaveGeofence(102)` | `leaveGeofence(101) == true \|\| leaveGeofence(102) == true` |
| `ignition && inGeofence(123)` | `ignition == true && inGeofence(123) == true` |
| `!inGeofence(123)` | `inGeofence(123) == false` |

`||` reads its operands from left to right. On a message where a call returns `null` before any call has returned `true`, the condition stops, and the geofences named after it are never tested. That message routes to `else`, and nothing reports why.

A single call used on its own as a `logic` node condition needs no `== true`, because `null` and `false` both take the `else` path. Add it anyway when a later node reads the boolean attribute of that node. With `== true` the node stores `false`, and without it the node stores nothing.

## Example

This `logic` node routes a message to `then` when the device has just left either warehouse. It keeps working on messages where one of the two calls can't answer:

```json
{
  "id": 2,
  "type": "logic",
  "data": {
    "title": "Left either warehouse",
    "name": "left_warehouse",
    "condition": "leaveGeofence(51577) == true || leaveGeofence(85269) == true"
  },
  "view": { "position": { "x": 350, "y": 50 } }
}
```

This `initiate_attributes` node stores the result of one geofence test as a boolean attribute. The `== true` comparison keeps the attribute a real `true` or `false`, even on a message with no position:

```json
{
  "id": 3,
  "type": "initiate_attributes",
  "data": {
    "title": "Zone flags",
    "items": [
      { "name": "in_delivery_zone", "value": "inGeofence(35229) == true" }
    ]
  },
  "view": { "position": { "x": 600, "y": 50 } }
}
```

## See also

* [Geofence name function](geofence-name.md) for `geofenceName()`, which returns the name of the geofence containing a position.
* [Nodes](nodes.md) for the full structure of the `logic` and `initiate_attributes` nodes.
* [Null propagation](../Technologies/navixy-iot-logic-expression-language/expression-syntax-reference.md#null-propagation) for what each operator does with a `null` operand.
* [Missing values in expressions](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/iot-logic/nodes/missing-values-in-expressions) in the Navixy user documentation.
* [Geofence functions](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/iot-logic/nodes/geofence-functions) in the Navixy user documentation, for the geofence picker in the flow builder.
