---
description: >-
  Read the name of the geofence containing a device position inside IoT Logic
  formulas and conditions with geofenceName(), including its parameters, return
  values, and the account feature it requires.
---

# Geofence name function

`geofenceName()` returns the name of the geofence that contains the position reported in the message being processed. The result is a string, so one call replaces a chain of per-geofence checks when a flow needs to know where a device is rather than whether it is inside one specific area.

The function is available in two node fields:

* `data.items[].value` of an `initiate_attributes` node, where the returned name becomes the value of a calculated attribute.
* `data.condition` of a `logic` node, where the returned name can be compared with a string.

`geofenceName()` is a paid option, and Navixy enables it for each dealer separately. Read [Availability](#availability) before you add it to a flow.

## Availability

`geofenceName()` requires the account feature `iot_logic_geofence_search`, which is disabled by default for every dealer. Navixy enables it on request for the dealer that owns your account. Quote the feature name when you ask for it.

Three conditions apply to the feature:

* Trial and demo accounts never receive it.
* A sub-dealer account doesn't inherit it from the parent dealer. Navixy enables it for the sub-dealer directly.
* No Admin Panel control switches it on. Only Navixy can enable it.

{% hint style="warning" %}
Without the feature, `flowCreate` and `flowUpdate` reject any flow that calls `geofenceName()`, and the flow isn't saved. See [Rejection when the feature is missing](#rejection-when-the-feature-is-missing).

Flows saved while the feature was active keep working, and every `geofenceName()` call in them returns `null` instead. See [Behavior in saved flows without the feature](#behavior-in-saved-flows-without-the-feature).
{% endhint %}

## Syntax

The function has five forms. Each omitted parameter takes its default value.

| Form | Geofences searched | Position used |
| --- | --- | --- |
| `geofenceName()` | All geofences of the account | Current position, gaps included |
| `geofenceName(group)` | Geofences tagged `group` | Current position, gaps included |
| `geofenceName(group, index)` | Geofences tagged `group` | Position at `index`, gaps included |
| `geofenceName(group, validation)` | Geofences tagged `group` | Current position, gap handling per `validation` |
| `geofenceName(group, index, validation)` | Geofences tagged `group` | Position at `index`, gap handling per `validation` |

No form takes `index` without `group`. To search all geofences at a historical position, pass an empty string as `group`, as in `geofenceName('', 1)`.

## Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `group` | String | All geofences | Name of a geofence tag. IoT Logic treats geofence tags as geofence groups, and the search covers only the geofences that have this tag. Matching ignores letter case. An empty string means all geofences. |
| `index` | Integer, 0 to 11 | `0` | Position of the message in the stored history: `0` is the current message, `1` the previous one, `11` the oldest stored one. |
| `validation` | `'all'` or `'valid'` | `'all'` | How to count history entries that hold no position. `'all'` counts them, so `index` addresses an exact history slot. `'valid'` skips them, so `index` addresses the nth position that exists. |

`index` and `validation` behave as they do in [`value()`](nodes.md#historical-data-access-index), because `geofenceName()` reads the `latitude` and `longitude` attributes of that history entry to get the point it tests.

Geofence tags come from the same tag list as every other tagged entity in the account. A tag that is assigned to no geofence is accepted at save time, and it returns `null` for every message.

An unrecognised `validation` value is rejected when you save the flow, with the message `The formula is invalid: [1:${column} JEXL error : geofenceName]`, where `${column}` is the position of the call in the formula. `index` is not checked, see the warning below.

## Return value

The function returns the geofence name as a string. It returns `null` in each of the following cases.

| Cause | Detail |
| --- | --- |
| The position is outside every searched geofence | The normal negative result |
| The history entry holds no position | `latitude` or `longitude` is absent at the requested `index`, or the pair isn't a valid coordinate |
| `index` is above 11 | The flow saves without an error, and the attribute stays empty for every message |
| The tag matches no geofence | Including a tag name that doesn't exist in the account |
| The account holds more than 15,000 geofences and the call passes no tag | See [Accounts with many geofences](#accounts-with-many-geofences) |
| The dealer doesn't have `iot_logic_geofence_search` | Only in flows saved while the feature was active |

A `null` result is not distinguishable from a device outside all geofences. When a formula needs a value in both cases, add a fallback with the `?:` operator:

```javascript
geofenceName() ?: 'Outside all geofences'
```

When `geofenceName()` returns `null`, this formula produces `Outside all geofences` instead of an empty value.

{% hint style="warning" %}
Only `value()` rejects an `index` above 11 at save time. `geofenceName()`, `genTime()`, and `srvTime()` accept any index, save without an error, and then return `null` for every message. The attribute is created and stays empty, and nothing reports a problem. Keep `index` within 0 to 11. See [Invalid value() arguments](../Technologies/navixy-iot-logic-expression-language/formula-errors.md#invalid-value-arguments).
{% endhint %}

## How the geofence search works

### Which geofences are searched

The search covers the geofences of the account that owns the GPS device sending the message. Geofences of other accounts are never searched.

All three geofence types take part: `circle`, `polygon`, and `sausage`. The name returned is the geofence label, the same value that [`zone/list`](https://navixy.com/docs/navixy-api/user-api/resources/tracking/zone#post-zone-list) returns in `label`.

### Overlapping geofences

When a position falls inside more than one searched geofence, the function returns the name of one of them. IoT Logic doesn't rank the candidates, so treat the choice as unspecified.

The choice is stable across consecutive messages. For each device and each tag, IoT Logic remembers the geofence matched for the previous message and tests it first, so a device that stays inside an overlap keeps reporting the same name. A message that leaves the remembered geofence starts the search again.

### Accounts with many geofences

On an account with more than 15,000 geofences, a call that passes no tag returns `null` for every message. IoT Logic builds a separate geofence search index for each tag and one index for the whole account, and it stops building the account-wide index above that number.

Tagged searches are unaffected, because each tag gets its own index sized by the tag rather than by the account. On a large account, tag the geofences the flow needs and always pass the tag:

```javascript
geofenceName('service-areas')
```

## Examples

Use these formulas in the `value` field of an `initiate_attributes` node item unless the description says otherwise.

| Formula | Result |
| --- | --- |
| `geofenceName()` | Name of any geofence containing the current position |
| `geofenceName('depots')` | Name of a geofence tagged `depots` containing the current position |
| `geofenceName('depots', 1)` | The same search against the position of the previous message |
| `geofenceName('', 0, 'valid')` | All geofences, tested against the most recent position that exists |
| `geofenceName() ?: 'In transit'` | Geofence name, or `In transit` when the device is outside all geofences |
| `geofenceName('depots') == 'Main depot'` | For a `logic` node condition: `true` only inside the geofence named `Main depot` |

The node below writes the current geofence name into an attribute named `current_zone`, and a readable label into `zone_label`:

```json
{
  "id": 2,
  "type": "initiate_attributes",
  "data": {
    "title": "Resolve current zone",
    "items": [
      { "name": "current_zone", "value": "geofenceName()" },
      { "name": "zone_label", "value": "geofenceName('depots') ?: 'Outside depots'" }
    ]
  },
  "view": { "position": { "x": 150, "y": 50 } }
}
```

Both attributes appear in Data Stream Analyzer and in the output data packet under those names. `current_zone` is empty for a message whose position is outside every geofence, and `zone_label` holds `Outside depots` for the same message.

A `logic` node can branch on the name. The condition below sends messages recorded inside any geofence tagged `restricted` down the THEN path:

```json
{
  "id": 3,
  "type": "logic",
  "data": {
    "title": "In a restricted area",
    "name": "in_restricted_area",
    "condition": "geofenceName('restricted') != null"
  },
  "view": { "position": { "x": 350, "y": 50 } }
}
```

The `!=` comparison is safe against `null`, because the equality operators always return a real `true` or `false`. The relational operators `<`, `<=`, `>`, and `>=` return `null` when an operand is `null`, so don't use them to test the result. See [Null propagation](../Technologies/navixy-iot-logic-expression-language/expression-syntax-reference.md#null-propagation).

## Rejection when the feature is missing

Without `iot_logic_geofence_search`, `flowCreate` and `flowUpdate` return HTTP 400 with internal code `292` (`IoT Flow Invalid`). Nothing is saved, and no partial update is applied.

The response names every node that adds the call. When the same formula also has a syntax problem, both errors arrive in the same `errors` array, so the feature error never hides a formula error:

```json
{
  "success": false,
  "status": {
    "code": 292,
    "description": "IoT Flow Invalid"
  },
  "errors": [
    {
      "node_ids": [2],
      "message": "Function geofenceName() in node \"Resolve current zone\" (#2) is not available for your account"
    }
  ]
}
```

### How to handle "Function geofenceName() is not available for your account"

Ask Navixy to enable `iot_logic_geofence_search` for the dealer that owns your account, or remove the call from the node named in the message and save again. `node_ids` gives the node IDs, so a flow editor can mark them.

The check reads the text of the node expressions, not the result of running them. Two consequences follow.

* A call in a branch that never runs still fails the save. A call in the second operand of a ternary expression, or after a `&&` that short-circuits, counts the same as a plain call.
* Whitespace doesn't hide a call. Both `geofenceName ()` and a line break before the bracket are detected.

A quoted string that only mentions the name is not a call, so a formula such as `'geofenceName() is unavailable'` saves normally.

### Editing a flow that already uses the function

When the feature is switched off after a flow was saved, the nodes that already hold the call stay editable. You can rename such a node, change its formula, and remove the call from it.

The exemption applies per node and only on `flowUpdate`, where the stored version of the same flow can be read. Adding the call to a node that doesn't already hold it is rejected. `flowCreate` grants no exemption, because there is no stored version to compare against.

### Behavior in saved flows without the feature

A saved flow keeps running after the feature is switched off. Every `geofenceName()` call returns `null`, and the rest of the flow is unaffected:

* Other attributes in the same node are calculated normally.
* The remaining part of a formula that mixes the call with other operations still produces a value.
* A fallback written by the flow author, such as `geofenceName() ?: 'unknown'`, returns the fallback.
* A `logic` node whose condition calls the function takes the ELSE path.

Nothing in the API response or in Data Stream Analyzer marks the difference between this state and a device outside all geofences.

## Best practices

* Pass a tag whenever the flow only needs a known set of geofences. The search costs less, and it keeps working on accounts above the 15,000-geofence limit.
* Add a `?:` fallback when the attribute feeds an external system that treats an empty value as an error.
* Compare with `==` or `!=` in a `logic` node condition, never with `<` or `>`, which return `null` for a `null` operand.
* Type the call by hand. Neither the formula autocomplete of the flow builder nor `flow/sources/attribute/list` offers `geofenceName`, and the geofence picker inserts only the ID-based geofence functions.
* Write the name as `geofenceName`. The spelling `geofence_name` is not accepted, and a formula that uses it fails validation with `unsolvable function/method 'geofence_name'`.
* Call the function directly. Wrapping it in `value()` does not work, because the first parameter of `value()` is an attribute name rather than an expression: `value("geofenceName()", 0, 'all')` looks for an attribute literally named `geofenceName()` and returns `null` for every message.

## See also

* [Nodes](nodes.md) for the full structure of the `initiate_attributes` and `logic` nodes.
* [Expression syntax reference](../Technologies/navixy-iot-logic-expression-language/expression-syntax-reference.md) for operators, data types, and null handling.
* [Formula error reference](../Technologies/navixy-iot-logic-expression-language/formula-errors.md) for every formula validation message.
* [Geofence functions](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/iot-logic/nodes/geofence-functions) in the Navixy user documentation for `inGeofence()`, `enterGeofence()`, and `leaveGeofence()`, which test one geofence by ID.
