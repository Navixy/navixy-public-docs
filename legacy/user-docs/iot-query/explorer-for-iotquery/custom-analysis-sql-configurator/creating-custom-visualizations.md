---
title: Creating custom visualizations
description: Create interactive charts from SQL query results using drag-and-drop configuration. Add filters, switch chart types, and arrange panels side by side
---

# Creating custom visualizations

Transform SQL query results into interactive charts through drag-and-drop operations. The visualization interface converts raw data into visual insights without coding requirements.

## Accessing the visualization interface

After executing SQL queries, two tabs appear above your results:

| **Tab**           | **Purpose**                          |
| ----------------- | ------------------------------------ |
| **Data**          | Review query results in table format |
| **Visualization** | Create interactive charts            |

Click **Visualization** to access the chart builder immediately after query execution.

## Interface overview

<figure><img src="../../.gitbook/assets/image-20250819-101042.png" alt="Query execution results showing Data and Visualization tabs with transition to chart builder"><figcaption></figcaption></figure>

The visualization workspace consists of three coordinated areas:

**Field List** (left panel):

* **Dimensions** - categorical data with document-style icons
* **Measures** - numerical data with hashtag icons
* **Time** - temporal fields for trend analysis

**Configuration Shelves** (center):

* **X-Axis/Y-Axis** - primary chart structure
* **Rows/Columns** - multi-panel comparisons
* **Filters** - data subsetting
* **Color/Size/Opacity** - visual encoding

**Canvas** (right):

* Real-time chart display with immediate updates
* Interactive zoom, pan, and exploration capabilities

## Essential controls

![](../../.gitbook/assets/image-20250819-101740.png)

Expand the section below to see key toolbar buttons that accelerate chart creation.

<details>

<summary>See main cotrols description</summary>

| **Control**                                                                                                                                                   | **Function**                                                    | **Usage**                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| <p><strong>Aggregation</strong><br><br><img src="../../.gitbook/assets/image-20250819-095023.png" alt="image-20250819-095023.png"></p>                        | Raw vs. aggregated data display                                 | Turn OFF to see individual records; keep ON to see totals/averages                    |
| <p><strong>Mark Types</strong><br><br><img src="../../.gitbook/assets/image-20250819-095030.png" alt="image-20250819-095030.png"></p>                         | Chart type selection (Bar, Line, etc.)                          | Switch between bar charts, line graphs, scatter plots based on your analysis needs    |
| <p><strong>Stack Mode</strong><br><br><img src="../../.gitbook/assets/image-20250819-095135.png" alt="image-20250819-095135.png"></p>                         | Create a Stack chart or Normalize a chart                       | Stack bars to show totals; normalize to compare percentages across categories         |
| <p><strong>Transpose</strong><br><br><img src="../../.gitbook/assets/image-20250819-095215.png" alt="image-20250819-095215.png"></p>                          | Switch the x-axis and y-axis of the chart                       | Flip chart orientation when categories are hard to read or compare                    |
| <p><strong>Sort Order</strong><br><br><img src="../../.gitbook/assets/image-20250819-095235.png" alt="image-20250819-095235.png"></p>                         | Sort in Ascending or Descending Order                           | Arrange data from highest to lowest values (or vice versa) to identify top performers |
| <p><strong>Axis Resizing</strong><br><br><img src="../../.gitbook/assets/image-20250819-095259.png" alt="image-20250819-095259.png"></p>                      | Resize the axes                                                 | Zoom into specific value ranges to examine data in more detail                        |
| <p><strong>Layout Mode</strong><br><br><img src="../../.gitbook/assets/image-20250819-095318.png" alt="image-20250819-095318.png"></p>                        | Resize the chart or use the auto-sized chart                    | Switch to manual sizing when you need larger charts for presentations                 |
| <p><strong>Exploration Mode</strong><br><br><img src="../../.gitbook/assets/image-20250819-095349.png" alt="image-20250819-095349.png"></p>                   | Explore data. You can choose either point mode or brush mode    | Select individual data points or drag to select multiple points for detailed analysis |
| <p><strong>Export</strong><br><br><img src="../../.gitbook/assets/image-20250819-095401.png" alt="image-20250819-095401.png"></p>                             | Save visualizations (PNG or SVG)                                | Download chart images for reports, presentations, or documentation                    |
| <p><strong>Export as CSV</strong><br><br><img src="../../.gitbook/assets/image-20250819-095721.png" alt="image-20250819-095721.png" data-size="original"></p> | Export visualized data in CSV format                            | Download the underlying data to analyze in Excel or other spreadsheet programs        |
| <p><strong>Export code</strong><br><br><img src="../../.gitbook/assets/image-20250819-095828.png" alt="image-20250819-095828.png" data-size="original"></p>   | Export visualization as code in Python or JSON (Graphic Walker) | Get code to recreate this exact chart in your own Python scripts                      |

</details>

## Building visualizations: sample workflow

**Example scenario**: Analyzing operational data with fields like `category_field`, `performance_metric`, `timestamp`, `region`.

{% stepper %}
{% step %}
**Create foundation chart**

* Drag categorical field (e.g., `category_field`) → **X-Axis**
* Drag numerical field (e.g., `performance_metric`) → **Y-Axis**
* Interface creates aggregated bar chart automatically
{% endstep %}

{% step %}
**Refine visualization type**

Click **Mark Types** button for alternatives:

* **Bar charts**: category comparisons
* **Line charts**: trend analysis over time
* **Scatter plots**: correlation analysis (turn off aggregation)
{% endstep %}

{% step %}
**Add analytical depth**

* Drag additional categorical field → **Color** shelf = visual differentiation
* Drag numerical field → **Size** shelf = proportional representation
* Multiple dimensions reveal hidden relationships
{% endstep %}
{% endstepper %}

\[SCREENSHOT: Progressive chart building showing basic bar chart evolving into multi-dimensional analysis]

## Advanced techniques

### Multi-panel analysis

Create comparative views using **Rows/Columns** shelves:

* Drag categorical field → **Rows** = separate panels for each category
* Consistent scales enable direct cross-comparisons
* Reveals patterns obscured in aggregated views

### Filtering and focus

Use **Filters** shelf for targeted analysis:

* Drag categorical fields for subset selection
* Drag numerical fields for range-based filtering
* Multiple filters combine for precise data exploration

### Interactive exploration

* Manual chart resizing using **Resize** button options
* Zoom and pan capabilities for detailed examination
* Export capabilities for sharing and integration

{% hint style="info" %}
**Performance tip**: Apply filters before complex visualizations to maintain responsiveness with large datasets.
{% endhint %}

## Evaluating your results

After creating custom visualizations, assess whether this approach meets your analytical needs:

**Visualization complexity**: Can you create the charts and insights required for your operational decisions?

**Data exploration depth**: Do the interactive capabilities provide sufficient analytical flexibility?

**Sharing and integration**: Do export options support your reporting and collaboration workflows?

**Performance and scalability**: Does the interface handle your data volume and complexity requirements effectively?

## Next steps

Based on your custom visualization experience, you may need more advanced capabilities. For comprehensive guidance on selecting appropriate business intelligence tools that match your specific requirements and organizational needs, see [Selecting BI tools](../../iot-query/connection-setup/selecting-bi-tools/).
