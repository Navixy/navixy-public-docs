---
description: Create, edit, and delete attributes in the Initiate Attribute node. Use autofill to build formula expressions quickly from available telemetry fields.
---

# Managing attributes

The **Initiate Attribute** node allows you to create, edit, and manage data attributes throughout your IoT Logic flow. This guide covers the basic attribute management actions (create, edit, delete) and essential operations, including autofill functionality for formula creation.

## Creating attributes

Here's how to create a new attribute in the Initiate attribute node:

{% stepper %}
{% step %}
### Add new attribute

Click the **Add attribute** button
{% endstep %}

{% step %}
### Name the attribute

Enter a name for your attribute (e.g., "Speed mph"). This name will be displayed in [Data Stream Analyzer](../../data-stream-analyzer.md) and act as a [custom sensor name in Navixy](displaying-new-calculated-attributes-on-the-navixy-platform.md).
{% endstep %}

{% step %}
### Define formula

Define the value expression. For faster formula building, use [autofill](managing-attributes.md#autofill-attribute-names).

{% hint style="info" %}
Short syntax is default for autofilled attribute names in formulas, and you can also use it for manual formula input. When referencing only the latest valid value of an attribute, you can omit the full `value()` function syntax and quotation marks. For example, the temperature conversion formula can be written as `temperature*1.8 + 32` instead of `value('temperature', 0, 'valid')*1.8 + 32`.
{% endhint %}
{% endstep %}

{% step %}
### Finalize configuration

Click **Save** to confirm the node configuration
{% endstep %}
{% endstepper %}

The new attribute is saved in the node and the configured calculation is applied immediately in the flow.

### Time settings for attributes

Time settings give you control over timestamps associated with your attributes:

* **Generation time**: When the data was created
  * Use `now()` to set the current time in milliseconds
  * Use `genTime('parameter_name', 0, 'valid')` to use the parameter's own generation time
* **Server time**: When the data was received by IoT Logic
  * Use `now()` to set the current time in milliseconds
  * Use `srvTime('parameter_name', 0, 'valid')` to use the parameter's server time
  * Add offsets to adjust for time zones (e.g., `srvTime('can_speed', 0, 'valid') + 120000` adds 2 minutes)

{% hint style="info" %}
Time settings are important for data analysis and synchronization. Proper time configuration ensures that your data maintains chronological integrity throughout the flow.
{% endhint %}

### Autofill attribute names

When creating calculation formulas, you need to reference existing attribute names from physical devices or calculated attributes from other nodes. To simplify this process and prevent misspellings, IoT Logic provides autofill functionality for attribute names.

To use autofill when building formulas:

1. Click ![image-20250605-130755.png](../../../../../.gitbook/assets/image-20250605-130755.png) in the **Formula** field.
2. Select the desired attribute from the appeared list, it supports manual text input for search purposes.
3. Click on the attribute name to insert it into your formula.

The selected attribute is automatically inserted in the ready-to-use format, using the [short syntax](managing-attributes.md#short-syntax) of Navixy IoT Logic Expression Language. It means that autofilled entries access any latest value of the selected attribute.

You can modify the parameters as needed - change the index number for historical values or adjust the validity flag. To do it, you need to define the formula using the [full syntax](managing-attributes.md#full-syntax) explicitly, inside the `value()` function.

The list is filtered based on your flow's data sources and matches what's visible in [Data Stream Analyzer](../../data-stream-analyzer.md).

{% hint style="info" %}
The autofill list displays only attributes that meet **all** of the following criteria simultaneously:

* **Supported by devices** - The attribute must be supported by at least one device currently added to your flow
* **Actually transmitted** - The device must have sent real data for this attribute (not just support it theoretically)
* **Available in flow** - This includes both device parameters and calculated attributes from other **Initiate Attribute** nodes within the same flow

**Example**: If your device supports both speed and temperature sensors but has only transmitted speed data (perhaps due to a faulty temperature sensor), the autofill list will show the 'speed' attribute but not 'temperature'. The temperature attribute will only appear once the device actually sends temperature readings, even though the device technically supports it.
{% endhint %}

#### Indexed attributes in autofill

Some data attributes contain multiple values indexed within a single attribute, such as readings from multiple analog sensors connected to a main telematics device. To use these attributes in calculations, you need to specify the exact index number corresponding to the specific sensor or input you want to reference.

For example, if you need to work with voltage readings from the third analog input on your device, this data comes through the attribute `analog_2` (using zero-based indexing where the first input is index 0). In your formula, this would appear as `value('analog_2', 0, 'valid')`.

Autofill handles this scenario for you as well:

* Indexed attributes are marked with ![image-20250606-123725.png](../../../../../.gitbook/assets/image-20250606-123725.png) icon in the autofill list.
* These entries display the available index range in square brackets, such as `analog_[1..4]` for attributes supporting five indexed values (indexes 1 through 4).
* When you select an indexed attribute, the cursor automatically positions at the end of the attribute name within the quotes, allowing you to immediately type the specific index number you need.

## Expression language

All formulas in IoT Logic follow the specifications of [Navixy IoT Logic Expression Language](https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-iot-logic-expression-language). Below, you find a brief reference to the language syntax.

### Short syntax

Short formula option accesses the latest attribute value, without checking validity. It is handy when you don't need historical values in a formula and don't want to filter out `null` values.

{% hint style="info" %}
[Autofill ](managing-attributes.md#autofill-attribute-names)feature always uses the short syntax.
{% endhint %}

**Syntax:** `attribute_name`

**Defaults:**

* Index: `0` (current value)
* Validation: `'all'` (includes null values)

For example, the short form `temperature` equals the full formula `value('temperature', 0, 'all')`. This means you can easily create calculations without adding the default parameters explicitly:

```jexl
temperature * 1.8 + 32
```

### Full syntax

Using full formula syntax allows you to access historical values or explicit validation mode. This helps when you need to use the non-latest values and handle nulls. In the case of full syntax, you need to define the complete `value()` function.

**Function:** `value(attribute_name, index, validation)`

**Parameters:**

<table><thead><tr><th width="152.54547119140625">Parameter</th><th width="176.45452880859375">Range/Values</th><th>Description</th></tr></thead><tbody><tr><td><code>attribute_name</code></td><td>Any valid attribute</td><td>Exact name from device telemetry (can be <a href="managing-attributes.md#autofill-attribute-names">autofilled</a>)</td></tr><tr><td><code>index</code></td><td>0-12</td><td>Historical position: 0=current, 1=previous, 12=12 readings ago</td></tr><tr><td><code>validation</code></td><td><code>'all'</code> or <code>'valid'</code></td><td><code>'all'</code>=includes nulls (exact index), <code>'valid'</code>=excludes nulls (Nth valid reading)</td></tr></tbody></table>

**Examples:**

* Any previous reading

```jexl
value('temperature', 1, 'all')
```

* 5th valid reading back

```jexl
value('speed', 5, 'valid')
```

* Temperature change (short and full syntaxes used in the same formula)

```jexl
temperature - value('temperature', 1, 'all')
```

{% hint style="info" %}
For more formula samples, see [Calculation examples](calculation-examples.md).
{% endhint %}

## Editing existing attributes

To modify an existing attribute:

1. Open the node configuration window by hovering your mouse over the node to display quick actions, or double-clicking the node
2. Find the attribute you wish to edit in the attribute list
3. Make the needed changes in the text fields of the attribute properties: **Attribute name**, **Formula** or time settings
4. If you need to edit other attributes in this node, repeat step 3 for them
5. Click **Save** to apply your changes to the node configuration

{% hint style="info" %}
When you edit an attribute, the changes will only apply to new data received after saving. Historical data already collected will not be recalculated.
{% endhint %}

## Deleting attributes

To remove an attribute that is no longer needed:

1. Open the node configuration window by hovering your mouse over the node to display quick actions, or double-clicking the node
2. Find the attribute you wish to remove and click <img src="../../../../../.gitbook/assets/image (12) (2).png" alt="" data-size="line">
3. Click **Apply changes** to confirm the node configuration

{% hint style="info" %}
When you delete an attribute, it will no longer be calculated for new data, but historical data containing this attribute will remain unchanged in the database.
{% endhint %}
