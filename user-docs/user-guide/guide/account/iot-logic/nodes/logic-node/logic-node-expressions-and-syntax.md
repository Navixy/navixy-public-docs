---
description: Write IF/THEN Logic expressions using the Navixy JEXL-based expression language. All expressions must return a boolean value to route data through the flow.
---

# IF/THEN Logic expressions and syntax

## Expression fundamentals

The IF/THEN Logic node uses the [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-iot-logic-expression-language), which is based on Java Expression Language (JEXL). All expressions must return a boolean value (true/false) for proper node operation.

**Expression evaluation**: Expressions are evaluated from left to right, and you can use parentheses to control the order of operations.

**Basic syntax example**:

```jexl
condition1 && (condition2 || condition3 > condition4)
```

## Available operators

### Comparison operators

<table><thead><tr><th width="138.54547119140625">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>==</code></td><td>Checks if two operands are equal. If operands are of different types, JEXL converts them to one if possible</td></tr><tr><td><code>!=</code></td><td>Checks for inequality of two operands. Returns true if operands are not equal</td></tr><tr><td><code>&#x3C;</code></td><td>Checks that the left operand is smaller than the right operand</td></tr><tr><td><code>&#x3C;=</code></td><td>Checks that the left operand is smaller or equal to the right operand</td></tr><tr><td><code>></code></td><td>Checks that the left operand is larger than the right operand</td></tr><tr><td><code>>=</code></td><td>Checks that the left operand is larger or equal to the right operand</td></tr></tbody></table>

### Logical operators

<table><thead><tr><th width="139.45458984375">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>&#x26;&#x26;</code> or <code>and</code></td><td>Logical AND - checks if two conditions are true. Returns true if both conditions are true</td></tr><tr><td><code>|</code> or <code>or</code></td><td>Logical OR - checking for the truth of at least one of the two conditions</td></tr><tr><td><code>!</code> or <code>not</code></td><td>Logical NOT - converts the result of the condition to the opposite value</td></tr></tbody></table>

### Pattern matching operators

<table><thead><tr><th width="138.54547119140625">Operator</th><th>Description</th></tr></thead><tbody><tr><td><code>=~</code></td><td>Checks if the value of the left operand is in the set of the right operand. For strings, checks for regex pattern match</td></tr><tr><td><code>!~</code></td><td>Checks if the value of the left operand is not in the set of the right operand. For strings, checks for regex pattern mismatch</td></tr><tr><td><code>=^</code></td><td>Checks that the left string operand starts with the right string operand</td></tr><tr><td><code>!^</code></td><td>Checks that the left string operand doesn't start with the right string operand</td></tr><tr><td><code>=$</code></td><td>Checks that the left string operand ends with the right string operand</td></tr><tr><td><code>!$</code></td><td>Checks that the left string operand doesn't end with the right string operand</td></tr></tbody></table>

### Geofence functions

Three functions evaluate a device's position relative to a named Navixy geofence. For usage instructions and examples, see [Geofence functions](geofence-functions.md).

<table><thead><tr><th width="194">Function</th><th width="137">Parameter</th><th>Returns true when</th></tr></thead><tbody><tr><td><code>inGeofence(id)</code></td><td>Geofence ID</td><td>The device is currently inside the geofence</td></tr><tr><td><code>enterGeofence(id)</code></td><td>Geofence ID</td><td>The device has just crossed into the geofence</td></tr><tr><td><code>leaveGeofence(id)</code></td><td>Geofence ID</td><td>The device has just crossed out of the geofence</td></tr></tbody></table>

**Short syntax:**

```jexl
inGeofence(35229 /* Delivery zone #4 */)
```

The geofence name appears as a comment for readability and has no effect on evaluation. The geofence picker inserts this format automatically.

**Long syntax** (for use with the `value()` function):

```jexl
value("inGeofence(35229 /* Delivery zone #4 */)", 1, valid)
```

The second parameter is the historical index (0 for the current packet, 1 for the previous), and the third is the validity filter, following the same convention as other `value()` calls.

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

## Error handling scenarios

| Scenario                           | Result             | Flow Path       | Attribute Value |
| ---------------------------------- | ------------------ | --------------- | --------------- |
| Expression evaluates to `true`     | Success            | THEN connection | `true`          |
| Expression evaluates to `false`    | Success            | ELSE connection | `false`         |
| Referenced attribute is `null`     | Treated as `false` | ELSE connection | `false`         |
| Syntax error in expression         | Treated as `false` | ELSE connection | `null`          |
| Referenced attribute doesn't exist | Treated as `false` | ELSE connection | `null`          |

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

For the full reference on geofence functions including `enterGeofence()` and long syntax, see [Geofence functions](geofence-functions.md).

</details>
