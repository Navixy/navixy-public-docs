---
description: "Human-readable explanations for all JEXL formula validation error messages in Navixy IoT Logic, with causes and corrected examples."
---

# Formula error reference

{% hint style="warning" %}
Formula error messages are always shown in English, regardless of the user's locale. The outer wrapper "The formula is invalid" is translated, but the reason in brackets is raw output from the JEXL library and is not translated.
{% endhint %}

This page maps each raw error message to its cause and shows how to correct the formula. For the full list of functions and operators available, see the [Expression syntax reference](expression-syntax-reference.md).

## Quick reference

| Error pattern | Meaning | Section |
| --- | --- | --- |
| `global assign/modify error in 'X'` | Tried to assign or mutate a value | [Assignment errors](#assignment-errors) |
| `create instance error in 'new(..., ...)'` | Used `new(...)` to create an object | [Disabled language features](#disabled-language-features) |
| `method call error in '.X(...)'` | Called a method with dot notation | [Disabled language features](#disabled-language-features) |
| `local variable error in '...'` | Used a lambda expression (`->`) | [Disabled language features](#disabled-language-features) |
| `parsing error in 'X'` | Syntax error near the indicated token | [Syntax errors](#syntax-errors) |
| `unsolvable function/method 'X'` | Function does not exist or wrong argument count | [Unknown or misused functions](#unknown-or-misused-functions) |
| `JEXL error : no such function namespace X` | Unknown function namespace | [Unknown or misused functions](#unknown-or-misused-functions) |
| `undefined property 'X'` | Array index or dot-property access on an attribute | [Property access errors](#property-access-errors) |
| `JEXL error : value` | Invalid arguments passed to `value()` | [Invalid value() arguments](#invalid-value-arguments) |

## Assignment errors

**Error pattern:** `1:N global assign/modify error in 'X'`

The expression language is read-only. You can read attribute values and compute results, but you cannot assign or change variables. This error appears when the formula contains an assignment or mutation operator.

The most common cause is using `=` to compare values when `==` is required.

| What was typed | Error | Fix |
| --- | --- | --- |
| `can_ignition_state = 1` | `global assign/modify error in 'can_ignition_state'` | `can_ignition_state == 1` |
| `speed = speed + 1` | `global assign/modify error in 'speed'` | Not possible; reference the current value directly |
| `power += 5` | `global assign/modify error in 'power'` | Not supported |
| `counter++` | `global assign/modify error in 'counter'` | Not supported |
| `--counter` | `global assign/modify error in 'counter'` | Not supported |

{% hint style="info" %}
Use `==` for equality comparisons, not `=`. Example: `can_ignition_state == 1`.
{% endhint %}

## Disabled language features

The expression language is a restricted subset of JEXL. Several Java and scripting features are intentionally disabled.

### Creating objects (`new`)

**Error pattern:** `1:N create instance error in 'new(..., ...)'`

The `new(...)` keyword for creating Java objects is not available.

| What was typed | Error |
| --- | --- |
| `new('java.lang.Long', 1)` | `create instance error in 'new(..., ...)'` |

### Method calls (dot notation)

**Error pattern:** `1:N method call error in '.X(...)'`

Calling methods directly on values using `.method()` syntax is disabled. Use `util:` namespace functions instead.

| What was typed | Error |
| --- | --- |
| `status.toString()` | `method call error in '.toString(...)'` |
| `ble_ids.size()` | `method call error in '.'size'(...)'` |

### Lambda expressions

**Error pattern:** `1:N local variable error in '...'`

Lambda expressions and local variable definitions using `->` are not available. The expression language does not support defining inline functions or local variables.

| What was typed | Error |
| --- | --- |
| `(x) -> x + 1` | `local variable error in '(x) -> x + 1'` |
| `x = y -> y` | `local variable error in 'x = y -> y'` |

## Syntax errors

**Error pattern:** `1:N parsing error in 'X'`

The formula cannot be parsed because of a syntax problem near the indicated token. The column number (`N`) and the token in quotes point to where parsing failed.

### Reserved keywords

The following keywords are not part of the expression language and produce a parsing error:

| What was typed | Error | Fix |
| --- | --- | --- |
| `var x = 5` | `parsing error in 'var'` | No variable declarations are supported |
| `for (i : [1,2,3]) i` | `parsing error in 'for'` | No loops are supported |
| `while (true) 1` | `parsing error in 'while'` | No loops are supported |
| `if (speed > 60) 1 else 0` | `parsing error in 'if'` | Use the ternary operator: `speed > 60 ? 1 : 0` |
| `return speed > 60` | `parsing error in 'return'` | No return statement; the expression result is the value |

### Incomplete or malformed expressions

| What was typed | Error | Cause |
| --- | --- | --- |
| `1+2+param1 42` | `parsing error in '42'` | Two values with no operator between them |
| `math:round(1` | `parsing error in '1'` | Missing closing parenthesis |
| `(1 + 2` | `parsing error in '2'` | Missing closing parenthesis |
| `1 2 3` | `parsing error in '2'` | Values with no operator between them |
| `* 5` | `parsing error in '*'` | Expression starts with an operator |
| `speed >` | `parsing error in '>'` | Operator with no right-hand operand |
| `speed > 60 and` | `parsing error in 'and'` | Condition without right-hand side |
| `speed =< 5` | `parsing error in '<'` | Invalid operator; use `<=` |
| `temperature > 30 AND humidity > 50` | `parsing error in 'AND'` | Operator keywords are lowercase: use `and` or `&&` |

### Incomplete ternary expressions

The ternary operator requires all three parts: `condition ? value_if_true : value_if_false`.

| What was typed | Error | Fix |
| --- | --- | --- |
| `speed > 60 ? 'fast'` | `parsing error in ''fast''` | Add `: 'slow'` to complete the expression |

### Missing closing parenthesis or trailing commas

| What was typed | Error | Fix |
| --- | --- | --- |
| `(speed > 60 and ignition == 1` | `parsing error in '1'` | Add the closing `)` |
| `util:checkBit(status, )` | `parsing error in ')'` | Remove the trailing comma: `util:checkBit(status, 0)` |

## Unknown or misused functions

### Wrong function name or argument count

**Error pattern:** `1:N unsolvable function/method 'X'` or `1:N unsolvable function/method 'X(arg, ...)'`

This error means the function does not exist, or was called with the wrong number of arguments. The error message shows the argument count as `(arg, arg, ...)` to indicate how many were passed.

| What was typed | Error | Fix |
| --- | --- | --- |
| `math:round()` | `unsolvable function/method 'round'` | Pass one argument: `math:round(speed)` |
| `math:round(1, 2)` | `unsolvable function/method 'round(arg, arg)'` | `math:round` takes exactly 1 argument |
| `math:unknown(1)` | `unsolvable function/method 'unknown(arg)'` | `unknown` is not a function in the `math:` namespace |
| `value()` | `unsolvable function/method 'value'` | Pass all 3 required arguments, or reference the attribute name directly |
| `value('speed', 0, 1, 'all')` | `unsolvable function/method 'value(arg, arg, arg, arg)'` | `value()` takes exactly 3 arguments |
| `util:bit(status)` | `unsolvable function/method 'bit(arg)'` | Pass a bit index too: `util:bit(status, 0)` |
| `inGeofence()` | `unsolvable function/method 'inGeofence'` | Pass a geofence ID: `inGeofence(123)` |

For all available functions and their correct signatures, see the [Expression syntax reference](expression-syntax-reference.md).

### Unknown namespace

**Error pattern:** `1:N JEXL error : no such function namespace X`

The namespace before the colon is not recognised. Only `util:` and `math:` namespaces are supported.

| What was typed | Error | Fix |
| --- | --- | --- |
| `unknown:round(1)` | `JEXL error : no such function namespace unknown` | Use `math:round(1)` or a `util:` function |

## Property access errors

**Error pattern:** `1:N undefined property 'X'`

This error appears when an expression tries to access a property of a value using array index notation (`[0]`) or dot notation (`.field`). Attribute values in the expression language are not objects or arrays; they are referenced by name directly.

| What was typed | Error | Fix |
| --- | --- | --- |
| `ble_ids[0]` | `undefined property '0'` | Reference the attribute by its full name (e.g., `ble_id_1`) |
| `status.field` | `undefined property 'field'` | Use the attribute name directly as provided by the device |

To extract parts of binary or numeric values, use `util:bit`, `util:bits`, `util:bytes`, or `util:hexToLong`. See [Bit-level operations](expression-syntax-reference.md#bit-level-operations).

## Invalid value() arguments

**Error pattern:** `1:N JEXL error : value`

This error comes from the platform's own validation of the `value()` function, not from the JEXL parser. The function call is syntactically correct, but one or more arguments are out of the allowed range.

| What was typed | Error | Problem |
| --- | --- | --- |
| `value('speed', -1, 'valid')` | `JEXL error : value` | Index `-1` is not valid; must be 0-12 |
| `value('speed', 0, 'bad')` | `JEXL error : value` | `'bad'` is not a recognised validation mode |
| `value('fuel_level', 0, 'last')` | `JEXL error : value` | `'last'` is not a recognised validation mode |

**Correct `value()` call:**

```
value(attribute_name, index, validation)
```

| Parameter | Valid values |
| --- | --- |
| `attribute_name` | String matching the device attribute name (case-sensitive) |
| `index` | Integer 0 to 12 (0 = current reading, 12 = oldest stored) |
| `validation` | `'all'` or `'valid'` |

For details on how each parameter behaves, see [Full syntax](expression-syntax-reference.md#full-syntax-historical-and-advanced) in the Expression syntax reference.

## Common mistakes

The table below lists frequent real-world mistakes along with the intended formula and its corrected version.

| What was typed | What they meant | Correct formula |
| --- | --- | --- |
| `speed => 5` | `speed >= 5` | `speed >= 5` |
| `speed =< 5` | `speed <= 5` | `speed <= 5` |
| `ignition = 1 and speed > 60` | `ignition == 1 and speed > 60` | `ignition == 1 and speed > 60` |
| `speed > 60 ? 'fast'` | `speed > 60 ? 'fast' : 'slow'` | `speed > 60 ? 'fast' : 'slow'` |
| `(speed > 60 and ignition == 1` | Missing closing `)` | `(speed > 60 and ignition == 1)` |
| `speed > 60 and` | Unfinished condition | `speed > 60 and fuel_level < 10` |
| `temperature > 30 AND humidity > 50` | Lowercase `and` | `temperature > 30 and humidity > 50` |
| `value(fuel_level, 0, 'all')` | `value('fuel_level', 0, 'all')` | `value('fuel_level', 0, 'all')` |
| `value('fuel_level', 0, 'last')` | Use `'all'` or `'valid'` | `value('fuel_level', 0, 'all')` |
| `util:bit(status)` | `util:bit(status, 0)` | `util:bit(status, 0)` |
| `util:checkBit(status, )` | Trailing comma after last argument | `util:checkBit(status, 0)` |
| `inGeofence()` | `inGeofence(123)` | `inGeofence(123)` |
| `if (speed > 60) 1 else 0` | Ternary expression | `speed > 60 ? 1 : 0` |
| `return speed > 60` | Just the expression result | `speed > 60` |
