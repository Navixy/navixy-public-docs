---
title: Connecting Grafana
description: Connect Grafana to IoT Query via the PostgreSQL data source. Build time series charts and dashboards for live and historical telematics monitoring
---

# Connecting Grafana

This guide demonstrates how to connect Grafana to **IoT Query** for visualizing telematics and fleet data. Grafana provides powerful visualization capabilities for monitoring vehicle status, sensor data, and other metrics in real-time.

{% hint style="info" %}
This guide is part of the **IoT Query** documentation suite and specifically covers connecting Power BI to your data warehouse. If you're still deciding which BI tool to use, refer to the [Selecting BI tools](https://www.navixy.com/docs/analytics/datahub/connection-setup/selecting-bi-tools) overview.
{% endhint %}

Watch a quick video overview to see what is possible with **IoT Query**+Grafana combination (available in English and Spanish):

{% tabs %}
{% tab title="English" %}
{% embed url="https://youtu.be/jGO3hIAjPCo?si=7bzWO4XrTwGnTq3c" %}
**IoT Query** + Grafana overview and setup tutorial.
{% endembed %}
{% endtab %}

{% tab title="Español" %}
{% embed url="https://youtu.be/jEf7i_mAWPE?si=pru7QyYHzyoqK79Q" %}
Descripción general y tutorial de configuración de **IoT Query** + Grafana.
{% endembed %}
{% endtab %}
{% endtabs %}

## Dashboard features

* Real-time sensor data visualization
* Time series analysis with customizable time ranges
* Dynamic filtering using variables
* Support for multiple data sources
* Interactive graphs and charts
* Custom query building with SQL

## Technical requirements

* Grafana 9.3 or later
* PostgreSQL data source plugin (included by default)
* Internet access for database connection
* Active **IoT Query** instance with connection credentials

## Installation and setup

Grafana must be installed on your system before you can connect to **IoT Query**.

{% stepper %}
{% step %}
#### Install Grafana

1. Download and install Grafana from the official website:\
   [https://grafana.com/grafana/download](https://grafana.com/grafana/download)
2. Follow the installation instructions for your operating system.
{% endstep %}

{% step %}
#### Download dashboard templates (optional)

Pre-built dashboard templates are available to help you get started quickly without building visualizations from scratch. These templates are maintained the [bi-intergrations repository](https://github.com/SquareGPS/bi-intergrations) under the `grafana` folder. You can clone the repository for access to ready-made dashboards:

```bash
git clone https://github.com/SquareGPS/bi-intergrations.git
```

Check the `grafana` folder for available dashboard JSON files.
{% endstep %}
{% endstepper %}

## Database connection

The Connections section in Grafana is where you manage all data source integrations.

{% stepper %}
{% step %}
#### Access the Connections tab

Open Grafana and navigate to **Connections** in the left sidebar. **IoT Query** runs on PostgreSQL, so you need to add a PostgreSQL data source enable Grafana to query your telematics data.
{% endstep %}

{% step %}
#### Add PostgreSQL data source

The PostgreSQL data source plugin comes pre-installed with Grafana and provides native support for connecting to PostgreSQL databases.

To add the data source:

1. In the Connections section, search for **PostgreSQL**
2. Click **Add new data source**
3. The PostgreSQL configuration page will open
{% endstep %}

{% step %}
#### Configure connection parameters

Your **IoT Query** connection details contain all the information needed to establish a secure connection. These parameters are unique to your instance and ensure Grafana can access your telematics data.

Locate your connection parameters in your Navixy account and the **IoT Query** welcome emai&#x6C;**.**

**Connection parameter reference**

| Parameter          | Configuration Field | Description                                                                   |
| ------------------ | ------------------- | ----------------------------------------------------------------------------- |
| Host               | `Host`              | Database server address provided in your **IoT Query** connection details     |
| Port               | `Port`              | Default is 5432 for PostgreSQL                                                |
| Database name      | `Database`          | Your assigned database name                                                   |
| Username           | `User`              | Your database username (referred to as "database user" in connection details) |
| Password           | `Password`          | Your secure database password                                                 |
| SSL mode           | `SSL Mode`          | Set to `require` for secure connections                                       |
| PostgreSQL version | `Version`           | Select **9.3** (recommended setting)                                          |
{% endstep %}

{% step %}
#### Configure additional settings

Beyond the essential connection parameters, Grafana offers additional configuration options. For most users, the default values are appropriate and will work correctly without modification. You can adjust these settings later based on your organization's security requirements, infrastructure constraints, or specific equipment needs.

Leave all other fields at their default values for initial setup. You don't need to enable any additional switches or toggles.
{% endstep %}

{% step %}
#### Test the connection

Testing the connection before saving ensures that all parameters are correct and that Grafana can successfully communicate with your **IoT Query** instance.

To verify and save:

1. Name your connection with a descriptive identifier (for example, "**IoT Query** Production" or "Fleet Analytics")
2. Click **Save & Test** to verify your connection
3. A green success message indicates the connection is working correctly
{% endstep %}
{% endstepper %}

## Creating your first visualization

Building your first visualization helps you understand how Grafana queries **IoT Query** and displays telematics data. This process involves creating a dashboard, adding a panel, configuring a query, and selecting an appropriate visualization format.

{% stepper %}
{% step %}
**Create a new dashboard**

Dashboards in Grafana serve as containers for multiple visualizations. Creating a dedicated dashboard helps you organize related visualizations and provides a central location for monitoring specific aspects of your fleet operations.

To create a dashboard:

1. Navigate to **Dashboards** in the left sidebar
2. Click **Create Dashboard**
3. Select **Add visualization**
4. Choose the PostgreSQL data source you just configured
{% endstep %}

{% step %}
**Configure the query**

Grafana provides two query modes: a visual Builder and a Code editor. For **IoT Query** connections, the Code editor gives you full control over SQL queries and is recommended for telematics data analysis.

To write your query:

1. In the query editor, switch from **Builder** to **Code** mode
2. Enter your SQL query directly

Example query for sensor data visualization:

```sql
SELECT 
    device_time AS time,
    value::numeric AS value,
    sensor_name
FROM raw_telematics_data.inputs
WHERE sensor_name = 'temperature'
    AND device_id = 12345
    AND $__timeFilter(device_time)
ORDER BY device_time
```

The query includes a time component (`device_time AS time`) which is essential for time series visualizations. The `$__timeFilter()` function is a Grafana macro that automatically applies the dashboard's selected time range to your query.

For more query examples, refer to the [SQL Recipe Book](https://www.navixy.com/docs/analytics/example-queries).
{% endstep %}

{% step %}
**Set visualization format**

The visualization format determines how your data appears on the dashboard. Different formats suit different types of data—time series work well for sensor readings over time, while tables are better for detailed records.

To configure the format:

1. Click **Run query** to verify your query returns data
2. Locate the visualization type selector (usually in the top-right of the panel)
3. Change the format from **Table** to **Time series** or your preferred format
4. Observe the data display to ensure it appears correctly

Time series visualizations require a time column and numeric values to function properly. Your query must return data in the appropriate format for the selected visualization type.
{% endstep %}

{% step %}
**Save your visualization**

Saving your work preserves the visualization configuration and makes it available for future reference. Regular saves are important—Grafana doesn't automatically save changes, so you could lose your work if you navigate away without saving.

To save a visualiyation:

1. Click **Save**
2. Provide a descriptive name for your dashboard
3. Select or create a folder for organization
4. Confirm the save operation

The dashboard is now available in your Grafana instance and can be accessed through the Dashboards menu.
{% endstep %}
{% endstepper %}

### Working with variables

Variables are one of Grafana's most powerful features for creating dynamic, interactive dashboards. Instead of hardcoding specific values like sensor names or device IDs in your queries, variables let users select different values from dropdown menus without editing the underlying SQL. This makes dashboards more flexible and easier to use.

{% stepper %}
{% step %}
**Access variable settings**

Variables are configured at the dashboard level and apply to all visualizations within that dashboard. You need to access the dashboard settings to create and manage variables.

To begin:

1. Open your dashboard
2. Click the dashboard **Settings** icon (gear icon) in the top-right corner
3. Navigate to the **Variables** section in the left panel
4. The section will be empty if no variables have been created yet
{% endstep %}

{% step %}
**Configure a variable**

Each variable needs a name, a data source, and a query that returns the possible values users can select. The variable values are populated dynamically by querying your **IoT Query** instance.

To create a variable:

1. Click **Add variable**
2. Configure the following settings:
   1. **General settings:**
      1. **Name**: `sensor_name` (use a clear, descriptive identifier without spaces)
      2. **Label**: Same as name for consistency, this is what users see on the dashboard
      3. **Type**: Select **Query** to populate values from database
   2. **Query options:**
      1. **Data source**: Select your PostgreSQL **IoT Query** connection from the dropdown
      2. **Query**: Enter a SQL query to populate variable values

Example query to list all sensors for a specific device:

```sql
SELECT DISTINCT sensor_name
FROM raw_telematics_data.inputs
WHERE device_id = 12345
ORDER BY sensor_name
```

After entering the query, Grafana executes it immediately and displays a preview of the returned values at the bottom of the configuration page. This confirms your query is working correctly and shows what options will be available in the dropdown.
{% endstep %}

{% step %}
**Apply variables in queries**

Once a variable is created, you need to update your visualization queries to reference it. Variables use special syntax with a dollar sign prefix, and Grafana automatically substitutes the selected value when executing queries.

To use the variable:

1. Open the visualization you want to make dynamic
2. Edit the query
3. Replace the hardcoded value with the variable reference

Example of updated query using the variable:

```sql
SELECT 
    device_time AS time,
    value::numeric AS value,
    sensor_name
FROM raw_telematics_data.inputs
WHERE sensor_name = '$sensor_name'
    AND device_id = 12345
    AND $__timeFilter(device_time)
ORDER BY device_time
```

Notice how `'temperature'` has been replaced with `'$sensor_name'`. The quotes are important for string values in SQL.
{% endstep %}

{% step %}
**Save and test**

Testing the variable ensures it works correctly and updates visualizations as expected. Make sure to save your work at each step to avoid losing configuration changes.

To complete the setup:

1. Click **Apply** to save the variable configuration
2. Click **Save dashboard** to preserve all changes
3. Return to the main dashboard view
4. Locate the variable dropdown at the top of the dashboard
5. Select different sensor names from the dropdown
6. Observe that the visualization updates automatically with data for the selected sensor

If the visualization doesn't update when you change the variable selection, verify that the variable name in your query exactly matches the variable name you created (variable names are case-sensitive).
{% endstep %}
{% endstepper %}

### Importing pre-built dashboards

Pre-built dashboards provide a fast way to start visualizing your data without building everything from scratch. These dashboards are designed by experienced users and include best practices for visualization, layout, and query optimization. However, they require configuration to connect to your specific **IoT Query** instance.

{% stepper %}
{% step %}
**Access the import function**

Grafana's import feature allows you to load dashboard configurations from JSON files or directly from Grafana.com using dashboard IDs.

1. Navigate to **Dashboards**
2. Click **New** → **Import**
3. An upload window will appear
{% endstep %}

{% step %}
**Import the dashboard**

The import process accepts dashboard configurations in multiple formats, giving you flexibility in how you load pre-built dashboards.

You can import dashboards using:

* **Dashboard ID** from Grafana.com
* **JSON file** downloaded from the bi-intergrations repository
* **JSON model** pasted directly

If using a JSON file:

1. Click **Upload JSON file**
2. Select the dashboard file
3. Provide a name and select the destination folder
4. Click **Import**

Wait for the dashboard template to finish uploading. Once complete, the dashboard will appear, but it won't display data yet because it's still connected to the original data source used when the template was created.
{% endstep %}

{% step %}
**Update data source connections**

This is a critical step. Imported dashboards are initially connected to external data sources that don't have access to your **IoT Query** data. You must update each visualization to use your PostgreSQL **IoT Query** connection.

To update visualizations:

1. Open the imported dashboard
2. Click **Edit** on the first visualization panel (look for the edit icon or three-dot menu)
3. In the query editor, locate the data source selector
4. Change the data source from the template's original source to your PostgreSQL **IoT Query** connection
5. Click **Run query** to verify the visualization retrieves data successfully
6. Click **Save** to preserve the changes
7. Repeat this process for each visualization panel on the dashboard

{% hint style="info" %}
Save your work frequently during this process. You may have multiple visualizations to update, and saving after each one ensures you don't lose progress.
{% endhint %}
{% endstep %}

{% step %}
**Update dashboard variables**

Many pre-built dashboards use variables for filtering and interactivity. These variables are also connected to the original data source and must be updated to query your **IoT Query** instance.

To update variables:

1. Open **Settings** → **Variables**
2. You'll see a list of all variables used in the dashboard
3. Click **Edit** on the first variable
4. Locate the **Data source** dropdown in the Query options section
5. Change it to your PostgreSQL **IoT Query** connection
6. Verify that the preview at the bottom shows the expected values from your database
7. Click **Apply** to confirm changes
8. Repeat for each variable in the list

After updating all variables, save the dashboard again. The variables should now populate with values from your **IoT Query** instance.
{% endstep %}

{% step %}
**Verify dashboard functionality**

Once you've updated all visualizations and variables, verify that everything works together correctly. This final check ensures the dashboard is fully functional and ready for regular use.

To verify:

1. Save the dashboard one final time using **Ctrl+S** or the **Save** button
2. Refresh the browser page completely (or use **F5**)
3. Check that all visualizations display data correctly
4. Test each variable dropdown to ensure it populates with values
5. Select different variable values and confirm visualizations update accordingly
6. Try different time ranges using the time picker

If any visualizations remain empty or show errors, return to the edit mode and verify the data source and query configuration for those specific panels.
{% endstep %}
{% endstepper %}

## Troubleshooting

### Database connection issues

**Connection error**: Verify credentials and connection parameters match your **IoT Query** details exactly. Check that host, port, database name, username, and password are correct.

**Firewall error**: Ensure your network allows connections to the specified host and port. Contact your network administrator if connection attempts are blocked.

**SSL/TLS error**: Verify SSL mode is set to `require` and your Grafana instance supports SSL connections.

### Query and visualization issues

**Empty visualization**: First, verify that the query actually returns data by checking the query inspector (click the "Query inspector" button in the panel editor). Ensure the selected time range includes periods when data exists in your database. Confirm the data source is correctly selected and connected. Check that table and column names in your query match the actual schema structure.

**Variable not working**: Verify that the variable query returns values by checking the preview in the variable configuration page. Ensure the variable reference in your query uses correct syntax (`$variable_name`) with no typos. Check that the variable is properly saved and applied to the dashboard. If the variable dropdown is empty, review the variable's SQL query for errors.

**Slow query performance**: Consider adding database indexes to frequently queried columns such as `device_time` and `device_id`. Limit data ranges by using time filters more restrictively. Optimize query structure by selecting only necessary columns instead of using `SELECT *`. Refer to the [SQL Recipe Book](https://www.navixy.com/docs/analytics/example-queries) for performance optimization examples and proven query patterns.

**Format mismatch**: Ensure your query returns data in the format expected by your visualization type. Time series panels require a column aliased as `time` and numeric value columns. Table panels accept any column structure. Graph panels need time and numeric data. Check the Grafana documentation for specific requirements of each visualization type.

### Import issues

**Missing data after import**: Verify that data source connections are updated for all panels and variables. Don't skip any panels during the update process. Check that schema names (`raw_business_data` or `raw_telematics_data`) specified in queries are correct for your **IoT Query** instance. Ensure table and column names match your actual database structure.

**Dashboard shows errors**: Open each panel's query editor and click "Query inspector" to see detailed error messages. Review error messages carefully—they often indicate specific issues like missing columns, incorrect data types, or syntax errors. Ensure field names in queries match your **IoT Query** schema exactly (field names are case-sensitive). Verify that data types returned by queries are compatible with the visualization type.

**Variables not populating**: Check that you've updated the data source for each variable, not just the visualization panels. Verify variable queries are syntactically correct and return data when executed directly. Ensure the variable type is set to "Query" rather than "Custom" or other types.

## Next steps

After successfully connecting Grafana to your **IoT Query** instance:

* Explore the available data schemas by reviewing the [Schema overview](../../schema-overview/) to understand data structure, relationships between tables, and available fields.
* Start with simple queries focused on specific business entities before building complex dashboards, this helps you learn the data structure and avoid performance issues.
* Review the [SQL Recipe Book](../../../example-queries/) for proven query patterns and optimization techniques, and examples of common telematics analytics scenarios
* Experiment with different visualization types (time series, tables, gauges, stat panels) to find the best representation for your specific data and use cases
* Consider creating multiple dashboards for different purposes: real-time monitoring, historical analysis, executive summaries, and operational reports.

### Support

For technical questions or assistance with **IoT Query** connections, contact support at [support@navixy.com](mailto:support@navixy.com).
