---
description: Practical examples for writing attribute calculations in the IoT Logic Expression Language. Includes guidance on attribute naming and handling null values safely.
---

# Calculation examples

The Initiate attribute node in IoT Logic supports a wide range of calculations through [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-iot-logic-expression-language). This guide provides practical examples of common calculations to help you extract maximum value from your IoT data.

## Important considerations

When creating calculations, keep these points in mind:

* **Attribute names validity**: Make sure you use correct attribute names in calculations. You can look up existing attribute names using [Data Stream Analyzer](../../data-stream-analyzer.md), or insert them with [#short-syntax](managing-attributes.md#short-syntax "mention") using [autocomplete](managing-attributes.md#autofill-attribute-names).
* **Data validity**: Ensure your expressions handle potential null values or invalid readings gracefully
* **Performance impact**: Complex calculations with many nested functions may impact processing speed for high-frequency data
* **Mathematical constraints**: Functions like logarithm and square root require positive input values
* **Historical references**: When using indexed values (e.g., `value('param', 1, 'valid')`), ensure you have sufficient historical data and use [full expression syntax](managing-attributes.md#full-syntax)

{% hint style="info" %}
Mathematical functions can be applied to any numeric parameter or previously calculated attribute. Always validate your calculations with test data before deploying to production flows.
{% endhint %}

## Basic calculations

### Unit conversions

Converting measurements between different units is one of the most common operations in IoT data processing.

#### **Speed conversion (km/h to mph)**

```jexl
can_speed/1.609
```

**Practical application:** This conversion standardizes vehicle speed data for regions using imperial measurements. By performing this calculation in IoT Logic rather than in downstream applications, you ensure consistency across all systems consuming the data.

**Temperature conversion (Celsius to Fahrenheit)**

```jexl
temperature * 1.8 + 32
```

**Practical application:** This conversion makes temperature readings comprehensible for users more familiar with Fahrenheit measurements. It's especially valuable for multinational organizations that operate across regions with different measurement standards.

### Calculating differences

Comparing current readings with previous values helps identify changes and trends in your data.

#### **Temperature change detection**

```jexl
temperature - value('temperature', 1, 'valid')
```

**Practical application:** This calculation helps detect rapid temperature fluctuations that might indicate equipment issues or environmental changes. By creating a dedicated attribute for this difference, you can set up alerts for sudden changes without complex downstream processing.

{% hint style="info" %}
**Example:** For a refrigerated truck fleet, this calculation can immediately identify when cargo temperature starts rising too quickly, allowing dispatchers to contact drivers before perishable goods are compromised. The attribute can trigger alerts when the temperature change exceeds 3 degrees in a short period, which might indicate a cooling system failure or door left open.
{% endhint %}

## Time-based calculations

Time calculations help you understand device behavior over time and measure operational patterns.

### **Finding time elapsed between last valid readings**

```jexl
srvTime('avl_25', 0, 'valid') - srvTime('avl_25', 1, 'valid')
```

**Practical application:** This calculation measures the interval between consecutive data transmissions, which can help identify communication issues or validate that devices are reporting at expected frequencies. This example showcases the calculation between the last two valid readings, but can be adjusted to handle any two historical values.

{% hint style="info" %}
**Example:** For a taxi fleet, this calculation can help identify connectivity issues with the GPS trackers. If the time between readings suddenly increases from the standard 30 seconds to several minutes, it might indicate poor cellular coverage areas or device malfunctions, allowing maintenance teams to address issues before they affect service quality.
{% endhint %}

### **Converting timestamp to human-readable format**

```jexl
dtFormat(genTime('can_fuel_1', 0, 'all'))
```

**Practical application:** Converting Unix timestamps to ISO 8601 format makes the data more readable in logs and reports. This format is widely supported by analysis tools and databases, simplifying integration with other systems.

## Advanced mathematical functions

IoT Logic supports sophisticated mathematical operations through its built-in math functions.

### Rounding values

**Round temperature to nearest integer**

```jexl
math:round(value('temperature_2', 0, 'valid'))
```

**Practical application:** Rounding is valuable when exact precision isn't needed or when you want to reduce noise in sensor readings. For instance, rounding environmental sensor data might be sufficient for general climate monitoring while reducing storage requirements. This function is also useful when creating categories or bands of values (e.g., grouping temperature readings into 5-degree increments).

{% hint style="info" %}
**Example:** In fleet management, engine temperature readings often include decimal points that aren't meaningful for driver alerts or maintenance scheduling. Rounding these values simplifies dashboard displays and makes temperature thresholds easier to define. For example, maintenance alerts can be triggered when the rounded engine temperature exceeds 90°C rather than dealing with precise values like 89.7°C.
{% endhint %}

### Logarithmic calculations

**Natural logarithm of a value**

```jexl
math:log(value('temperature_2', 0, 'valid'))
```

**Practical application:** Logarithmic transformations are particularly useful for:

* Compressing data that spans multiple orders of magnitude into a more manageable range
* Converting exponential relationships into linear ones for easier analysis
* Working with certain sensor types that have logarithmic response characteristics
* Sound level calculations, where decibels are logarithmic units
* pH measurements in environmental monitoring

{% hint style="info" %}
**Example:** When analyzing driver behavior with an accelerometer, the logarithmic function can help normalize acceleration data that ranges from slight movements to sudden braking events. This makes it easier to create a meaningful driver safety score that doesn't get overly skewed by occasional extreme events. Another practical example is when working with fuel sensors that don't provide linear readings. Some fuel level sensors have non-linear resistance values that correspond to the actual fuel level. Using logarithmic calculations can help translate these readings into more accurate fuel level percentages.
{% endhint %}

### Square root operations

**Calculate square root of a reading**

```jexl
math:sqrt(value('temperature_2', 0, 'valid'))
```

**Practical application:** Square root functions are valuable for:

* Converting between power and amplitude in electrical measurements
* Calculating standard deviations in statistical analysis of sensor data
* Determining root mean square (RMS) values for electrical parameters
* Distance calculations in multi-dimensional space (e.g., trilateration for positioning)
* Normalizing certain types of sensor data

{% hint style="info" %}
**Example:** When calculating the true distance between two GPS points (latitude and longitude), you need to use the square root function as part of the distance formula. For example, if you're calculating the distance a vehicle has traveled between two GPS readings using the Euclidean distance formula, the square root function is essential. Another common application is in calculating the magnitude of vibration or shock events from multiple accelerometer axes (X, Y, Z). The square root of the sum of squared values from each axis gives you the overall vibration magnitude, which is useful for detecting vehicle impacts or rough road conditions.
{% endhint %}

## Combined operations

You can create even more complex calculations by combining multiple functions and operations.

### **Geometric mean with rounding**

{% code overflow="wrap" %}
```jexl
math:round(math:sqrt(value('temperature_1', 0, 'valid') * value('temperature_2', 0, 'valid')))
```
{% endcode %}

**Practical application:** This calculation computes the geometric mean of readings from two temperature sensors and rounds the result to provide a cleaner value. The geometric mean is often more appropriate than an arithmetic average when dealing with rates, ratios, or measurements where multiplication is the natural way to combine values.

{% hint style="info" %}
**Example:** In a refrigerated transport scenario, you might have multiple temperature sensors placed throughout the cargo area. This calculation provides a balanced temperature reading that accounts for both values while minimizing the influence of extreme outliers. Rather than triggering alerts based on just one sensor (which might be affected by door openings), this combined calculation gives a more representative overall temperature condition for the sensitive cargo.
{% endhint %}

### **Standardized value calculation**

{% code overflow="wrap" %}
```jexl
(value('sensor_reading', 0, 'valid') - value('sensor_min', 0, 'valid')) / (value('sensor_max', 0, 'valid') - value('sensor_min', 0, 'valid')) * 100
```
{% endcode %}

**Practical application:** This normalizes a sensor reading to a percentage scale from 0-100%, allowing for standardized comparisons across different sensors with varied ranges. This is particularly useful for creating uniform dashboards or triggering alerts based on relative values rather than absolute measurements.

{% hint style="info" %}
**Example:** A fleet manager oversees vehicles from multiple manufacturers, each with different fuel sensor readings (ohms, volts, raw digital values). By normalizing these diverse readings to a consistent percentage scale, the manager can apply the same low-fuel alerts across the entire fleet and generate standardized fuel consumption reports without having to account for the specific sensor characteristics of each vehicle model.
{% endhint %}

## String operations

String operations let you combine multiple attribute values into a single string output.

### Joining attribute values

Combining values from multiple attributes is useful when you need to consolidate data from several sources into one field for display or transmission.

```jexl
util:joinNonNull(', ', ble_name_1, ble_name_2, ble_name_3, ble_name_4, ble_name_5, ble_name_6, ble_name_7, ble_name_8)
```

**Practical application:** Devices with BLE scanning capability can detect multiple peripheral sensors simultaneously, but not all sensor slots are always occupied. This calculation produces a clean, comma-separated list of active sensors, skipping unpaired slots automatically. The result is ready for display or forwarding to external systems without additional cleanup.

{% hint style="info" %}
**Example**: A refrigerated truck supports up to eight BLE temperature sensors placed throughout the cargo area, but only three are currently installed. Instead of producing "Sensor A, Sensor B, Sensor C, , , , , " with empty entries for the unused slots, `util:joinNonNull` outputs "Sensor A, Sensor B, Sensor C", a clean list that updates automatically as sensors are added or removed. If you need empty positions preserved in the output, use `util:join` instead, which treats null values as empty strings rather than skipping them.
{% endhint %}
