---
description: Write IF/THEN Logic expressions using the Navixy JEXL-based expression language. Expressions route data by evaluating to true or false, though a missing value can behave differently.
---

# IF/THEN Logic expressions and syntax

## Expression fundamentals

The IF/THEN Logic node uses the [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-iot-logic-expression-language), based on Java Expression Language (JEXL). Expressions are meant to return `true` or `false`, but a missing value doesn't always follow that rule. Some operators route a missing value to THEN instead of ELSE, and some leave the stored attribute `null` rather than `false`. See [Missing values and null routing](#missing-values-and-null-routing) before relying on a comparison to fail safely.

**Expression evaluation**: Expressions are evaluated from left to right, and you can use parentheses to control the order of operations.

**Basic syntax example**:

```jexl
condition1 && (condition2 || condition3 > condition4)
```

## Available operators

### Comparison operators

<table><thead><tr><th width="138.54547119140625">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>==</code></td><td>Checks if two operands are equal. If operands are of different types, JEXL converts them to one if possible</td></tr><tr><td><code>!=</code></td><td>Checks for inequality of two operands. Returns true if operands are not equal</td></tr><tr><td><code>&#x3C;</code></td><td>Checks that the left operand is smaller than the right operand</td></tr><tr><td><code>&#x3C;=</code></td><td>Checks that the left operand is smaller or equal to the right operand</td></tr><tr><td><code>></code></td><td>Checks that the left operand is larger than the right operand</td></tr><tr><td><code>>=</code></td><td>Checks that the left operand is larger or equal to the right operand</td></tr></tbody></table>

For how missing values affect routing, see [Missing values and null routing](#missing-values-and-null-routing).

### Logical operators

<table><thead><tr><th width="139.45458984375">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>&#x26;&#x26;</code> or <code>and</code></td><td>Logical AND - checks if two conditions are true. Returns true if both conditions are true</td></tr><tr><td><code>&#x7C;&#x7C;</code> or <code>or</code></td><td>Logical OR - checking for the truth of at least one of the two conditions</td></tr><tr><td><code>!</code> or <code>not</code></td><td>Logical NOT - converts the result of the condition to the opposite value</td></tr></tbody></table>

### Pattern matching operators

<table><thead><tr><th width="138.54547119140625">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>=~</code></td><td>Checks if the value of the left operand is in the set of the right operand. For strings, checks for regex pattern match</td></tr><tr><td><code>!~</code></td><td>Checks if the value of the left operand is not in the set of the right operand. For strings, checks for regex pattern mismatch</td></tr><tr><td><code>=^</code></td><td>Checks that the left string operand starts with the right string operand</td></tr><tr><td><code>!^</code></td><td>Checks that the left string operand doesn't start with the right string operand</td></tr><tr><td><code>=$</code></td><td>Checks that the left string operand ends with the right string operand</td></tr><tr><td><code>!$</code></td><td>Checks that the left string operand doesn't end with the right string operand</td></tr></tbody></table>

### Geofence functions

Three functions evaluate a device's position relative to a named Navixy geofence, and a fourth one returns the name of the geofence the device is in. For usage instructions and examples, see [Geofence functions](../geofence-functions.md).

<table><thead><tr><th width="194">Function</th><th width="137">Parameter</th><th>Returns true when</th></tr></thead><tbody><tr><td><code>inGeofence(id)</code></td><td>Geofence ID</td><td>The device is currently inside the geofence</td></tr><tr><td><code>enterGeofence(id)</code></td><td>Geofence ID</td><td>The device has just crossed into the geofence</td></tr><tr><td><code>leaveGeofence(id)</code></td><td>Geofence ID</td><td>The device has just crossed out of the geofence</td></tr></tbody></table>

**Current position:**

```jexl
inGeofence(35229 /* Delivery zone #4 */)
```

The geofence name appears as a comment for readability and has no effect on evaluation. The geofence picker inserts this format automatically.

**Historical position:**

```jexl
inGeofence(35229 /* Delivery zone #4 */, 1, 'valid')
```

The second parameter is the index of the data packet (0 for the current one, 1 for the previous one, up to 11), and the third is the validity filter, following the same convention as `value()`. Both are optional, and both apply to `inGeofence()` only. `enterGeofence()` and `leaveGeofence()` take the geofence ID and nothing else. Don't wrap a geofence function inside `value()`: the first parameter of `value()` is an attribute name, not an expression.

## Expression examples

<details>

<summary>Basic condition examples</summary>

**Equality checks**:

```
value('lock_state', 0, 'valid') == 'sealed'
door_state_2 == 0
```

**Inequality checks**:

```
value('lock_state', 1, 'valid') != 'unknown'
avl_io_221 != null
```

**Missing-value caution**: `!=` against a non-null literal routes missing values to THEN, not ELSE.

Safe: a presence guard limits the check to packets that carry the attribute.

```
value('temperature', 0, 'all') != null && value('temperature', 0, 'all') > 50
```

Unsafe: this fires on `temperature`'s first packets before it has any history, since `!=` treats a missing value as unequal to any literal.

```
value('temperature', 1, 'all') != 75
```

**Numeric comparisons**:

```
value('humidity', 1, 'all') < 80
value('humidity', 1, 'all') <= 80
value('humidity', 0, 'valid') > 80
value('humidity', 0, 'valid') >= 80
```

**Temperature monitoring**:

```
value('temperature', 0, 'valid') > 75
```

This expression triggers when temperature exceeds 75 degrees, useful for overheating alerts.

**Speed violation detection**:

```
value('speed', 0, 'valid') > 80
```

This expression identifies when vehicles exceed 80 km/h speed limits.

**Device health monitoring**:

```
value('battery_voltage', 0, 'valid') < 11.5
```

This expression detects low battery conditions requiring maintenance attention.

**Fuel level alerts**:

```
value('fuel_level', 0, 'valid') < 20
```

This expression identifies when fuel levels drop below 20%, enabling proactive refueling.

</details>

<details>

<summary>Logical operator examples</summary>

**AND operations**:

```
value('temperature', 0, 'valid') > 15 && value('humidity', 0, 'valid') > 80
value('temperature', 0, 'valid') > 15 and value('humidity', 0, 'valid') > 80
```

**OR operations**:

```
temperature < 10 || humidity > 80
temperature < 10 or humidity > 80
```

**NOT operations**:

```
!condition
not condition
```

</details>

<details>

<summary>Pattern matching examples</summary>

**Set membership**:

```
value('lock_state', 0, 'valid') =~ ['locked','unlocked']
value('driver_name', 0, 'valid') !~ ['John', 'Steve']
```

**String pattern matching**:

```
driver_id =^ 'cc6f8216'
driver_id !^ 'cc6f8216'
value('engine_hours', 0, 'valid') =$ '1000'
value('driver_id', 0, 'valid') !$ '8b38851c3c68'
```

</details>

<details>

<summary>Complex multi-condition examples</summary>

**After-hours speeding alert**:

```
value('speed', 0, 'valid') > 60 && (value('current_hour', 0, 'valid') >= 18 || value('current_hour', 0, 'valid') <= 6)
```

This combines speed monitoring with time-based conditions for enhanced safety oversight during night shifts.

**Comprehensive device diagnostics**:

```
value('gps_satellites', 0, 'valid') >= 4 && value('battery_voltage', 0, 'valid') > 11.5 && value('signal_strength', 0, 'valid') > -80
```

This validates multiple device health parameters simultaneously to ensure reliable operation.

**Driver safety monitoring**:

```
value('harsh_braking', 0, 'valid') == true && value('driver_identified', 0, 'valid') == false
```

This identifies unsafe driving behavior when the driver isn't properly identified in the system.

**Equipment maintenance scheduling**:

```
value('engine_hours', 0, 'valid') > 250 && value('last_maintenance', 0, 'valid') > 30
```

This triggers maintenance alerts when engine hours exceed thresholds and maintenance is overdue.

**Temperature range compliance**:

```
value('cargo_temperature', 0, 'valid') < -18 || value('cargo_temperature', 0, 'valid') > 4
```

This detects when refrigerated cargo temperatures fall outside the acceptable range.

</details>

<details>

<summary>Expression complexity and parentheses</summary>

You can create complex expressions by combining multiple conditions with parentheses to control evaluation order:

**Complex safety validation**:

```
driver_identified == false && (vibration_active == true || speed > 3)
```

**Multi-parameter equipment check**:

{% code overflow="wrap" %}
```
(value('oil_pressure', 0, 'valid') < 20 || value('coolant_temp', 0, 'valid') > 95) && value('engine_running', 0, 'valid') == true
```
{% endcode %}

</details>

## Missing values and null routing

**Common symptom:** A condition fires on a device's very first packets, before the referenced attribute has ever arrived. This usually means the condition uses `!=` (or a negated pattern operator) against a missing value. See [Comparison operators with null operands](#comparison-operators-with-null-operands) and [Logical operators with null operands](#logical-operators-with-null-operands) for which operators are safe by default and which need a guard.

A referenced attribute resolves to `null` in four cases:

- It has never been sent for this device.
- It's absent from the current packet, but an earlier packet still holds a value.
- The requested history index doesn't exist yet.
- The device sent nothing for more than 30 days, so Navixy dropped its stored history.

JEXL treats all four the same way, as `null`, so an operator's behavior on a missing value doesn't depend on which of the four caused it. A never-sent attribute doesn't error the flow. For the 30-day retention of stored values, see [Full syntax](../initiate-attribute-node/managing-attributes.md#full-syntax).

For why a missing value stops an expression, and for the comparison patterns that prevent it in both node types, see [Missing values in expressions](../missing-values-in-expressions.md).

Every incoming message leaves the Logic node through exactly one branch, THEN or ELSE, even when the condition references missing data. If the condition's overall result is anything other than `true` (including `null`, or a value the node can't evaluate), the message routes to ELSE. The ELSE path therefore carries both "condition is false" and "condition could not be satisfied from available data."

### Null-safe condition patterns

**Presence guard** (recommended when a condition must apply only to packets that carry the attribute). The stored attribute is a real `true`/`false` here, not `null`:

```jexl
value('temperature', 0, 'all') != null && value('temperature', 0, 'all') > 50
```

**Explicit missing-data check**: `null` literals are accepted in conditions.

```jexl
value('attribute', 0, 'all') == null
```

**`||` chain ordering**: Put the term that may be null last. Ordering helps only on packets where the left operand is true, so treat it as an improvement rather than a fix.

Unsafe: the null left operand routes to ELSE even though `speed > 50` is true on the same packet.

```jexl
value('attribute', 0, 'all') > 5 || value('speed', 0, 'all') > 50
```

Better: a true left operand short-circuits before the null right operand is evaluated. On a packet where the speed is 20, the null right operand is still evaluated, and the condition still routes to ELSE.

```jexl
value('speed', 0, 'all') > 50 || value('attribute', 0, 'all') > 5
```

For a form that works on every packet, compare each term explicitly. See [Compare a value explicitly](../missing-values-in-expressions.md#compare-a-value-explicitly).

**Don't rely on `!()` to detect missing data**: When `x` is null, both `x > 5` and `!(x > 5)` route to ELSE.

**`'all'` vs `'valid'`**: `value(attr, 0, 'valid')` can return the last non-null reading from an earlier packet. The condition may still evaluate against a real value even when the current packet is missing it. Use `'all'` when the condition must reflect the current packet only.

### Comparison operators with null operands

| Situation | Routes to | Stored attribute value |
| --- | --- | --- |
| `<`, `<=`, `>`, or `>=` against a missing value | ELSE | `null` |
| `!=` against a missing value and a non-null literal (e.g., `value('a', 0, 'all') != 1`) | THEN | `true` |
| `==` against a missing value and a non-null literal | ELSE | `false` |
| `== null` against a missing value | THEN | `true` |
| `!= null` when the attribute is absent | ELSE | `false` |
| `!= null` when the attribute is present | THEN | `true` |
| `=~` against a missing value and a string literal or a list | ELSE | `false` |
| `!~` against a missing value and a string literal or a list | THEN | `true` |
| `=^` or `=$` against a missing `value()` result | ELSE | `false` |
| `!^` or `!$` against a missing `value()` result | THEN | `true` |
| `=^`, `!^`, `=$`, or `!$` against a missing attribute referenced by name | ELSE | `null` |

{% hint style="warning" %}
`!=` against a missing value and a non-null literal evaluates to `true` and routes to THEN. A condition like `value('attribute', 1, 'all') != 1` can fire on a device's first packets, before that attribute has any history. This can happen even when the intent is to route unknown values to ELSE.

To fail safely when a value may not exist yet, use a presence guard or an explicit `== null` check:

```jexl
value('attribute', 1, 'all') != null && value('attribute', 1, 'all') != 1
```

`null == null` is `true`, and `null != null` is `false`. The guard checks whether the attribute has a value on this packet, not whether it differs from a literal.
{% endhint %}

{% hint style="warning" %}
`=~` and `!~` resolve to a real `true` or `false` on a missing value, whichever syntax you use. On a missing value, `=~` counts as "not a match" and lands on ELSE, and `!~` lands on THEN.

The starts-with and ends-with operators (`=^`, `!^`, `=$`, `!$`) are the one place where the two syntax options differ. With `value()`, they behave like `=~` and `!~`: `value('a', 0, 'all') =^ 'AB'` resolves to `false` and lands on ELSE, and `!^` resolves to `true` and lands on THEN. With the attribute referenced by name, `a =^ 'AB'` resolves to `null` instead, and both the positive and the negated form land on ELSE.

The short-hand form is what [autofill](../initiate-attribute-node/managing-attributes.md#autofill-attribute-names) inserts, so a condition built with the attribute picker takes the second path. Write the comparison with `value()` when you need `!^` or `!$` to fire on a packet that doesn't carry the attribute.

`!~` reaching THEN is a different mechanism from `!=` reaching THEN. `!~` lands there because a missing value counts as "not a match", and `!=` lands there because a missing value is "unequal" to the literal. Don't assume that one explains the other.
{% endhint %}

### Logical operators with null operands

| Situation | Routes to | Stored attribute value |
| --- | --- | --- |
| `!(x > 5)` when `x` is missing | ELSE | `null` |
| Null operand in `||` evaluated before a true operand on the same packet | ELSE | `null` |
| True operand in `||` evaluated before a null operand (short-circuits) | THEN | `true` |
| Null operand in `&&` with a true operand, either order | ELSE | `null` |
| `false` operand in `&&` evaluated before a null operand (short-circuits) | ELSE | `false` |
| `false` operand in `||` evaluated before a null operand | ELSE | `null` |

Order matters for both operators. Each one evaluates the left operand first and stops there when the left operand already settles the result. A `false` left operand settles `&&`, and a `true` left operand settles `||`. In every other combination the null operand is evaluated, the condition resolves to `null`, and the message routes to ELSE.

A true operand can't short-circuit past a null one in `&&`, since AND still needs to know whether the null side would fail the condition. A `false` operand can, and the node then stores a real `false` rather than `null`. For the same rule applied to attribute formulas, and for the comparison patterns that avoid it, see [Missing values in expressions](../missing-values-in-expressions.md).

## Error handling scenarios

| Scenario | Result | Flow Path | Attribute Value |
| --- | --- | --- | --- |
| Expression evaluates to `true` | Success | THEN connection | `true` |
| Expression evaluates to `false` | Success | ELSE connection | `false` |
| Referenced attribute is `null` and used alone, with no comparison operator | Resolves to `null` | ELSE connection | `null` |
| Missing value compared with `<`, `<=`, `>`, or `>=` | Resolves to `null` | ELSE connection | `null` |
| Missing value compared with `==` and a non-null literal | Evaluates to `false` | ELSE connection | `false` |
| Missing value compared with `== null` | Evaluates to `true` | THEN connection | `true` |
| Missing value compared with `!=` and a non-null literal | Evaluates to `true` | THEN connection | `true` |
| Syntax error in expression | Resolves to `null` | ELSE connection | `null` |

{% hint style="warning" %}
A row that routes to ELSE doesn't always mean the attribute is stored as `false`. A bare null reference and the relational operators (`<`, `<=`, `>`, `>=`) route to ELSE but store the attribute as `null`. A downstream expression that checks the attribute directly (`my_flag == false` or `my_flag == 0`) needs a null-safe guard to catch that case, since it won't match a `null` value.

In an **Initiate Attribute** node the same failures leave the attribute out of the outgoing data packet instead. See [Missing values in expressions](../missing-values-in-expressions.md).
{% endhint %}

## Practical implementation examples

<details>

<summary>Fleet temperature monitoring</summary>

**Business requirement**: Monitor refrigerated vehicles to ensure cargo temperature compliance

```
value('cargo_temperature', 0, 'valid') > 4 || value('cargo_temperature', 0, 'valid') < -18
```

* **THEN path**: Send immediate alerts to dispatch, log compliance violations, trigger corrective actions
* **ELSE path**: Continue normal processing for compliant temperatures, update status dashboards

</details>

<details>

<summary>Driver safety enforcement</summary>

**Business requirement**: Identify unsafe driving patterns during active shift hours

```
value('harsh_acceleration', 0, 'valid') == true && value('shift_active', 0, 'valid') == true
```

* **THEN path**: Generate driver coaching reports, send safety notifications, log incidents
* **ELSE path**: Process normal driving behavior data, update performance metrics

</details>

<details>

<summary>Predictive maintenance alerts</summary>

**Business requirement**: Detect potential equipment failures before they occur

```
value('engine_temperature', 0, 'valid') > 95 && value('oil_pressure', 0, 'valid') < 30
```

* **THEN path**: Schedule maintenance appointments, send technician alerts, log diagnostic data
* **ELSE path**: Continue routine monitoring, update equipment health dashboards

</details>

<details>

<summary>Geofence compliance monitoring</summary>

**Business requirement**: Ensure vehicles operate within authorized areas during business hours

```jexl
inGeofence(51577 /* Austin Warehouse */) == true && value('business_hours', 0, 'valid') == true
```

This expression uses the `inGeofence()` function to check whether a device is inside a named geofence, combined with a business-hours attribute. The geofence boundaries are managed in the Navixy geofences interface, so no coordinate values need to be maintained in the expression.

The `== true` comparison keeps the condition working on packets where the geofence function can't decide, such as a packet that carries no position. Without it, one empty value stops the whole condition and the message routes to ELSE.

* **THEN path**: Continue normal operations; device is in the authorized area during working hours.
* **ELSE path**: Generate an unauthorized location alert, notify security, and log the violation.

To detect the moment a vehicle leaves the authorized area rather than checking continuously, use `leaveGeofence()` instead:

```jexl
leaveGeofence(51577 /* Austin Warehouse */) == true && value('business_hours', 0, 'valid') == true
```

For the full reference on geofence functions including `enterGeofence()`, `geofenceName()`, and testing an earlier position with `inGeofence()`, see [Geofence functions](../geofence-functions.md).

</details>
