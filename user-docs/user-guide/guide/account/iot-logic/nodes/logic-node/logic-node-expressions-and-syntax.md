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

<table><thead><tr><th width="139.45458984375">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>&#x26;&#x26;</code> or <code>and</code></td><td>Logical AND - checks if two conditions are true. Returns true if both conditions are true</td></tr><tr><td><code>|</code> or <code>or</code></td><td>Logical OR - checking for the truth of at least one of the two conditions</td></tr><tr><td><code>!</code> or <code>not</code></td><td>Logical NOT - converts the result of the condition to the opposite value</td></tr></tbody></table>

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

The second parameter is the index of the data packet (0 for the current one, 1 for the previous one, up to 11), and the third is the validity filter, following the same convention as `value()`. Both are optional. Don't wrap a geofence function inside `value()`: the first parameter of `value()` is an attribute name, not an expression.

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
!driver_identified && (vibration_active || speed > 3)
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

A referenced attribute resolves to `null` in three cases:

- It has never been sent for this device.
- It's absent from the current packet, but an earlier packet still holds a value.
- The requested history index doesn't exist yet.

JEXL treats all three the same way, as `null`, so an operator's behavior on a missing value doesn't depend on which of the three caused it. A never-sent attribute doesn't error the flow.

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

**`||` chain ordering**: Put the term that may be null last.

Unsafe: the null left operand routes to ELSE even though `speed > 50` is true on the same packet.

```jexl
value('attribute', 0, 'all') > 5 || value('speed', 0, 'all') > 50
```

Safe: the true left operand short-circuits before the null right operand is evaluated.

```jexl
value('speed', 0, 'all') > 50 || value('attribute', 0, 'all') > 5
```

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
| `=~`, `=^`, or `=$` against a missing value and a string literal | ELSE | `false` |
| `!~`, `!^`, or `!$` against a missing value and a string literal | THEN | `true` |

{% hint style="warning" %}
`!=` against a missing value and a non-null literal evaluates to `true` and routes to THEN. A condition like `value('attribute', 1, 'all') != 1` can fire on a device's first packets, before that attribute has any history. This can happen even when the intent is to route unknown values to ELSE.

To fail safely when a value may not exist yet, use a presence guard or an explicit `== null` check:

```jexl
value('attribute', 1, 'all') != null && value('attribute', 1, 'all') != 1
```

`null == null` is `true`, and `null != null` is `false`. The guard checks whether the attribute has a value on this packet, not whether it differs from a literal.
{% endhint %}

{% hint style="warning" %}
The pattern-matching operators resolve to a real `true`/`false` on a missing value, never `null` like the relational operators. The negated forms (`!~`, `!^`, `!$`) land on THEN because a missing value counts as "not a match" on the positive form. That's a different mechanism from `!=`, which lands on THEN because a missing value is "unequal" to the literal. The two operator families reach THEN for different reasons, so don't assume one explains the other.
{% endhint %}

### Logical operators with null operands

| Situation | Routes to | Stored attribute value |
| --- | --- | --- |
| `!(x > 5)` when `x` is missing | ELSE | `null` |
| Null operand in `||` evaluated before a true operand on the same packet | ELSE | `null` |
| True operand in `||` evaluated before a null operand (short-circuits) | THEN | `true` |
| Null operand in `&&` with a true operand, either order | ELSE | `null` |

Unlike `||`, operand order doesn't change the outcome for `&&`. A true operand can't short-circuit past a null one, since AND still needs to know whether the null side would fail the condition.

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
inGeofence(51577 /* Austin Warehouse */) && value('business_hours', 0, 'valid') == true
```

This expression uses the `inGeofence()` function to check whether a device is inside a named geofence, combined with a business-hours attribute. The geofence boundaries are managed in the Navixy geofences interface, so no coordinate values need to be maintained in the expression.

* **THEN path**: Continue normal operations; device is in the authorized area during working hours.
* **ELSE path**: Generate an unauthorized location alert, notify security, and log the violation.

To detect the moment a vehicle leaves the authorized area rather than checking continuously, use `leaveGeofence()` instead:

```jexl
leaveGeofence(51577 /* Austin Warehouse */) && value('business_hours', 0, 'valid') == true
```

For the full reference on geofence functions including `enterGeofence()`, `geofenceName()`, and testing an earlier position, see [Geofence functions](../geofence-functions.md).

</details>
