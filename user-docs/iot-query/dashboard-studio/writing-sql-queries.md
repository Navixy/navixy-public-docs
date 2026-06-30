---
title: Writing SQL queries
description: Write PostgreSQL queries optimized for Dashboard Studio visualizations. Learn data access patterns, layer selection, and performance best practices
---

# Writing SQL queries

Dashboard Studio uses SQL to retrieve data from IoT Query schemas. You write SQL in two contexts: panel editors, where statements power visualizations, and the standalone SQL Editor for data exploration. This page explains how to write effective SQL for both contexts, with emphasis on visualization requirements since they have specific structural constraints.

### Where SQL is used

Dashboard Studio provides two SQL environments for different purposes. Understanding when to use each helps you work more efficiently.

[**Visualization queries**](writing-sql-queries.md#how-to-write-sql-for-visualizations) power individual panels in reports. You write these statements in the panel editor's **SQL Query** tab. Each panel runs one statement that must return data in a specific structure matching the visualization type. These statements execute when reports load or refresh, so performance matters for user experience. Visualization SQL cannot modify data; all statements run as read-only SELECT operations against IoT Query schemas.

**Reports** use the same visualization SQL approach as dashboard panels. A report runs one query that powers three views simultaneously: the data table, chart, and location map. The statement must return all columns needed across all three components, so include coordinate, time, and metric columns together in a single SELECT.

[**SQL Editor**](writing-sql-queries.md#how-to-use-the-sql-editor) supports data exploration and export. Access the SQL Editor from the left sidebar under Tools. Write any SELECT statement to examine data structure, validate assumptions, or export results as CSV. The SQL Editor shows full result tables with column sorting and provides execution metrics. Use this for testing logic before adding SQL to visualization panels, or for ad-hoc data extraction that doesn't need visualization.

{% hint style="info" %}
**The key difference**: visualization SQL must match exact column structures, while SQL Editor statements can return any result format. Test complex logic in SQL Editor first, then adapt it for visualizations.&#x20;
{% endhint %}

### How to write SQL for visualizations

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Visualization SQL must return specific column counts and data types. Dashboard Studio cannot render a bar chart from three columns or a stat tile from text data. Check the Dataset Requirements section in the SQL Query tab to see exactly what your chosen visualization expects before writing the statement. The table below contains supported visualization types:

| Visualization                                  | Query requirement            | Example                                                           |
| ---------------------------------------------- | ---------------------------- | ----------------------------------------------------------------- |
| [Stat tile](writing-sql-queries.md#stat-tiles) | Single numeric value         | `SELECT COUNT(*) FROM schema.table`                               |
| [Bar chart](writing-sql-queries.md#bar-charts) | Two columns: category, value | `SELECT column1, COUNT(*) FROM schema.table GROUP BY column1`     |
| [Pie chart](writing-sql-queries.md#pie-charts) | Two columns: label, value    | `SELECT category, SUM(value) FROM schema.table GROUP BY category` |
| [Table](writing-sql-queries.md#tables)         | Any columns                  | `SELECT column1, column2, column3 FROM schema.table`              |
| [Text](writing-sql-queries.md#text-panels)     | No query required            | Markdown content only                                             |

<details>

<summary>Stat tiles</summary>

Stat tiles display single numeric values. Statements must return exactly one row with one numeric column:

{% code title="Total trips in current month" overflow="wrap" %}
```sql
SELECT COUNT(*) as value
FROM silver.trips
WHERE start_time >= DATE_TRUNC('month', CURRENT_DATE);
```
{% endcode %}

{% code title="Total distance traveled (km)" overflow="wrap" %}
```sql
SELECT SUM(distance_km) as value
FROM silver.trips
WHERE start_time >= CURRENT_DATE - INTERVAL '7 days';
```
{% endcode %}

The column name doesn't matter, only that the result is a single numeric value. Dashboard Studio displays this value with formatting you configure in Visualization Settings.

</details>

<details>

<summary>Bar charts</summary>

Bar charts require exactly two columns: category (text or date) and value (numeric). The first column becomes the X-axis, the second becomes bar heights:

{% code title="Trips per vehicle type" overflow="wrap" %}
```sql
SELECT 
  vehicle_type as category,
  COUNT(*) as value
FROM silver.trips
WHERE start_time >= DATE_TRUNC('month', CURRENT_DATE)
GROUP BY vehicle_type
ORDER BY value DESC;
```
{% endcode %}

{% code title="Daily trip counts" overflow="wrap" %}
```sql
SELECT 
  DATE_TRUNC('day', start_time)::date as category,
  COUNT(*) as value
FROM silver.trips
WHERE start_time >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY DATE_TRUNC('day', start_time)
ORDER BY category;
```
{% endcode %}

Use `ORDER BY` to control the bar sequence. Sort by value for ranked comparisons or by category for time-series progressions.

</details>

<details>

<summary>Pie charts</summary>

Pie charts require exactly two columns: label (text) and value (numeric). The first column becomes slice labels, the second determines slice sizes:

{% code title="Trip distribution by zone" %}
```sql
SELECT 
  zone_name as label,
  COUNT(*) as value
FROM silver.zone_visits
WHERE enter_time >= DATE_TRUNC('month', CURRENT_DATE)
GROUP BY zone_name
ORDER BY value DESC
LIMIT 10;
```
{% endcode %}

Add LIMIT clauses for categories with many values. Pie charts with 20+ slices become unreadable; limit to top 10-15 categories.

</details>

<details>

<summary>Tables</summary>

Tables accept any number of columns with any data types. Select the columns you want to display:

{% code title="Recent trip details" %}
```sql
SELECT 
  device_id,
  start_time,
  end_time,
  distance_km,
  duration_minutes,
  max_speed_kmh
FROM silver.trips
WHERE start_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY start_time DESC
LIMIT 100;
```
{% endcode %}

Column names become table headers. Use aliases with spaces for readable headers: `distance_km as "Distance (km)"`.

</details>

<details>

<summary>Text panels</summary>

Text panels display single text values or formatted strings. Statements must return one text column:

{% code title="Last data update timestamp" %}
```sql
SELECT 
  'Last updated: ' || MAX(record_added_at)::text as value
FROM bronze.tracking_data_core;
```
{% endcode %}

</details>

Report queries follow the same structural rules as visualization queries in dashboard panels. Because a single statement powers the data table, chart, and location map together, you may need to combine columns that would be written as separate panel queries in a dashboard. For example, a bar chart panel query returning two columns is not sufficient for a report that also needs GPS coordinates for the location map. Include all required columns for every component in one statement. The core filtering and JOIN logic remains the same as in panel queries; only the SELECT clause needs to be wider.

### How to write SQL for reports

A report runs one SQL query that powers three components simultaneously: the data table, chart, and location map. Unlike dashboard panels, where each panel has its own focused query, a report query must return all columns needed across every component in a single SELECT statement.

#### Column requirements per component

Each report component has specific column requirements. Your query must satisfy all components you have enabled.

| Component    | Required columns                                                  | Notes                                            |
| ------------ | ----------------------------------------------------------------- | ------------------------------------------------ |
| Data table   | Any columns                                                       | All returned columns appear as table columns     |
| Chart        | At least one time or category column, at least one numeric column | Axis columns are selected in the chart settings  |
| Location map | Latitude and longitude as decimal degrees                         | Dashboard Studio auto-detects coordinate columns |

Because the data table accepts any columns, it imposes no additional constraints. The chart and location map drive most of the structural decisions.

#### Combining components in one query

A query that returns only the columns needed for a chart (two columns: category and value) cannot also power a location map. You must include all required columns together.

The following example returns columns for all three components: a time column and numeric column for the chart, coordinate columns for the location map, and additional attributes that appear in the data table.

```sql
SELECT
    t.device_id,
    o.object_label,
    t.device_time,
    t.latitude::float / 10000000 AS latitude,
    t.longitude::float / 10000000 AS longitude,
    t.speed::float / 100 AS speed
FROM raw_telematics_data.tracking_data_core t
JOIN raw_business_data.objects o ON t.device_id = o.device_id
WHERE t.device_time >= NOW() - INTERVAL '24 hours'
ORDER BY t.device_time DESC
LIMIT 1000
```

In this query, `device_time` and `speed` serve the chart, `latitude` and `longitude` serve the location map, and all columns appear in the data table.

{% hint style="info" %}
The raw telematics tables store coordinates and speed as scaled integers. Coordinates are divided by 10,000,000 (10⁷) to convert to decimal degrees, and speed is divided by 100 (10²) to convert to km/h. Apply these conversions in any query that reads from `raw_telematics_data` tables.
{% endhint %}

#### Adapting dashboard panel queries for reports

Any panel query from a dashboard is a valid starting point for a report. The adjustment needed depends on which components you want to enable.

If the panel query is already a table visualization returning multiple columns, it may already include everything needed. Add coordinate columns if the location map is required.

If the panel query is a bar chart or stat tile query returning aggregated results, it likely lacks the row-level detail needed for the data table and location map. In that case, remove the aggregation and work from the underlying raw or Silver layer data instead.

[SQL Recipe Book](../example-queries/) contains ready-to-use query examples for common fleet analyses. Recipes from the book can be adapted for reports by adding coordinate columns where the location map is needed. The core WHERE and JOIN logic transfers directly; adjust only the SELECT clause to cover all required components.

### How to use global variables

Global variables provide reusable values across multiple SQL statements. Define variables in **Settings > Configuration > Global Variables**, then reference them using `${variable_name}` syntax.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Define variables for values that change periodically but remain consistent across multiple panels: analysis date ranges, vehicle type filters, or threshold values. When these values change, update the variable definition once instead of editing individual SQL statements.

{% code title="Using date range variables" %}
```sql
SELECT 
  DATE_TRUNC('day', start_time)::date as category,
  COUNT(*) as value
FROM silver.trips
WHERE start_time >= '${analysis_start_date}'::date
  AND start_time < '${analysis_end_date}'::date
GROUP BY DATE_TRUNC('day', start_time)
ORDER BY category;
```
{% endcode %}

Variables store text values. Cast them to appropriate types in SQL: `'${variable_name}'::date` for dates, `'${variable_name}'::integer` for numbers.

For statement-specific parameters that change frequently, you can use CTE parameter blocks at the start:

```sql
WITH params AS (
  SELECT 
    5 as min_idle_minutes,
    10 as max_idle_speed_kmh,
    '${analysis_start_date}'::date as date_from,
    '${analysis_end_date}'::date as date_to
)

SELECT 
  device_id,
  COUNT(*) as idle_count,
  SUM(duration_minutes) as total_idle_minutes
FROM silver.idle_events e
CROSS JOIN params p
WHERE e.event_time >= p.date_from
  AND e.event_time < p.date_to
  AND e.speed_kmh <= p.max_idle_speed_kmh
  AND e.duration_minutes >= p.min_idle_minutes
GROUP BY device_id
ORDER BY total_idle_minutes DESC;
```

This pattern combines global variables (date ranges) with statement-specific parameters (thresholds), keeping all adjustable values at the top for easy maintenance.

### How to access IoT Query schemas

IoT Query organizes data in Raw data, Transformation, and Insight layers. Understanding which layer to use saves time and improves SQL clarity. For complete schema details, see the [IoT Query Schema Overview](https://www.navixy.com/docs/analytics/iotquery/schema-overview).

**Raw data layer** contains raw tracking points from devices: `bronze.tracking_data_core` stores every GPS position with timestamps, coordinates, and sensor readings. Use Raw data for point-level analysis or when you need raw sensor values not processed into higher layers.

**Transformation layer** provides processed entities: `silver.trips` aggregates tracking points into trip records with start/end times, distance, and duration. `silver.zone_visits` records when devices enter and exit geofences. `silver.idle_events` identifies periods when vehicles remain stationary with engines running. Use Transformation for most visualization needs since it provides analysis-ready structures.

**Insight layer** offers pre-aggregated metrics and dimensional models for complex analytics. Use Insight for fleet-wide statistics or multi-dimensional analysis that would require complex joins against Silver tables.

Reference tables using `schema.table` format: `silver.trips`, not just `trips`. Include date range filters in WHERE clauses to limit data scanned:

{% code title="Always filter by time ranges" %}
```sql
SELECT device_id, COUNT(*) as trip_count
FROM silver.trips
WHERE start_time >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY device_id;
```
{% endcode %}

Most SQL statements filter by device, time range, or both. Add these filters early in WHERE clauses to reduce data volume processed.

### How to use the SQL Editor

Access SQL Editor from the left sidebar under Tools. Use it for three main purposes: testing logic before adding to panels, exploring data schemas to understand available columns, and exporting data that doesn't need visualization.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

The SQL Editor supports multiple tabs for different statements. Write SQL in tabs, execute with the "Execute Query" button, and view results in the table below. Results show execution metrics (execution time, rows returned) and support column sorting for quick data examination.

Export results as CSV using the "Export CSV" button. This works for ad-hoc reports or data extracts for external analysis. The SQL Editor has no result row limit, unlike visualization SQL which should return focused datasets.

Test visualization SQL in the SQL Editor before adding to panels. Write the statement, verify it returns expected columns and data types, then copy it to the panel editor's SQL Query tab. This workflow catches structural issues before you configure visualization settings.

Exploration pattern for new data:

{% code expandable="true" %}
```sql
-- 1. Examine table structure
SELECT * FROM silver.trips LIMIT 10;

-- 2. Check date range coverage
SELECT 
  MIN(start_time) as earliest,
  MAX(start_time) as latest,
  COUNT(*) as total_trips
FROM silver.trips;

-- 3. Test filtering logic
SELECT 
  device_id,
  start_time,
  distance_km
FROM silver.trips
WHERE start_time >= '2024-01-01'
  AND device_id = 12345
ORDER BY start_time;

-- 4. Adapt for visualization (2 columns for bar chart)
SELECT 
  DATE_TRUNC('day', start_time)::date as day,
  COUNT(*) as trips
FROM silver.trips
WHERE start_time >= '2024-01-01'
  AND device_id = 12345
GROUP BY DATE_TRUNC('day', start_time)
ORDER BY day;
```
{% endcode %}

### Common SQL patterns

Most visualization SQL follows similar patterns. Copy these structures and adjust filters, columns, and aggregations for your specific needs.

<details>

<summary><strong>Time-series counts</strong> for tracking trends</summary>

```sql
SELECT 
  DATE_TRUNC('hour', start_time) as time_bucket,
  COUNT(*) as event_count
FROM silver.trips
WHERE start_time >= CURRENT_DATE - INTERVAL '24 hours'
GROUP BY DATE_TRUNC('hour', start_time)
ORDER BY time_bucket;
```

</details>

<details>

<summary><strong>Category rankings</strong> for comparing groups</summary>

```sql
SELECT 
  category_column,
  COUNT(*) as count
FROM schema.table
WHERE filter_conditions
GROUP BY category_column
ORDER BY count DESC
LIMIT 15;
```

</details>

<details>

<summary><strong>Metric calculations</strong> for aggregated statistics</summary>

```sql
SELECT 
  SUM(distance_km) as total_distance,
  AVG(duration_minutes) as avg_duration,
  COUNT(*) as trip_count
FROM silver.trips
WHERE start_time >= DATE_TRUNC('week', CURRENT_DATE);
```

</details>

<details>

<summary><strong>Filtered summaries</strong> with multiple conditions</summary>

```sql
SELECT 
  device_id,
  COUNT(*) as trips,
  SUM(distance_km) as total_km
FROM silver.trips
WHERE start_time >= '${period_start}'::date
  AND start_time < '${period_end}'::date
  AND distance_km >= 5
  AND duration_minutes >= 10
GROUP BY device_id
HAVING COUNT(*) >= 5
ORDER BY total_km DESC;
```

</details>

### What to do when SQL fails

Execution failures fall into three categories: structural mismatches with visualization requirements, SQL syntax errors, or filters that return no data.

#### **Column structure mismatches**&#x20;

Occur when results don't match visualization expectations. If you selected a bar chart but your SQL returns three columns, Dashboard Studio cannot render it. Check Dataset Requirements in the SQL Query tab. The bar chart needs exactly two columns (category, value), so adjust your SELECT clause:

```sql
-- Wrong: three columns
SELECT device_id, start_time, COUNT(*) FROM silver.trips GROUP BY device_id, start_time;

-- Correct: two columns
SELECT device_id, COUNT(*) as trips FROM silver.trips GROUP BY device_id;
```

#### **SQL syntax errors**&#x20;

Show specific error messages. Common issues include missing schema prefixes (`trips` instead of `silver.trips`), typos in column names, or incorrect date casting. Test statements in SQL Editor to see detailed error messages with line numbers.

#### **Empty results**&#x20;

Despite successful execution indicate filters exclude all data. Test the SQL without WHERE clauses in SQL Editor to verify the table contains data, then add filters incrementally to identify which condition excludes your expected results.

#### Performance issues

If statements execute slowly or timeout, add date range filters to WHERE clauses. Operations scanning entire tables process millions of rows unnecessarily:

```sql
-- Slow: no date filter
SELECT device_id, COUNT(*) FROM silver.trips GROUP BY device_id;

-- Fast: date range filter
SELECT device_id, COUNT(*) 
FROM silver.trips 
WHERE start_time >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY device_id;
```

For additional performance guidance, see [How to access IoT Query schemas](writing-sql-queries.md#how-to-access-iot-query-schemas) for best practices on filtering and schema selection.

### Where to find SQL examples

The [SQL Recipe Book](../example-queries/) provides complete examples for common telematic analyses. These recipes demonstrate patterns for trip analysis, zone visit calculations, idle detection, and fleet metrics. Each recipe includes the complete SQL statement, explanation of logic, and sample results.

Adapt Recipe Book examples for visualizations by adjusting the SELECT clause to match visualization requirements. A recipe that returns detailed trip records can become a bar chart by adding GROUP BY and COUNT aggregation. A statement calculating per-vehicle metrics can become a stat tile by adding SUM across all vehicles.

You just need to:

1. Copy examples from [Recipe Book](../example-queries/) to the Dashboard Studio'sEditor.&#x20;
2. Test with your actual data.
3. Verify results, then modify the SELECT clause for your target visualization.&#x20;

The core WHERE and JOIN logic remains the same; you adjust only the output structure.

For schema details, see the [IoT Query Schema Overview](https://www.navixy.com/docs/analytics/iotquery/schema-overview). This reference explains available tables, column definitions, and relationships between Raw data, Transformation, and Insight layers.
