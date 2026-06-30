---
title: Connecting Power BI
description: Connect Power BI to IoT Query via the PostgreSQL connector. Query live telematics data and build interactive fleet monitoring reports and dashboards
---

# Connecting Power BI

In this article, we will walk through the process of connecting Microsoft Power BI to **IoT Query**. To make it more illustrative, we will also build a dashboard that connects to the analytical database and monitors the real-time status of vehicles, all using Microsoft Power BI.

{% hint style="info" %}
This guide is part of the **IoT Query** documentation suite and specifically covers connecting Power BI to your data warehouse. If you're still deciding which BI tool to use, refer to the [Selecting BI tools](./) overview.
{% endhint %}

## Dashboard features

* Display total number of vehicles
* Visualize vehicle movement statuses (moving/stopped/parked)
* Visualize connection statuses (active/idle/offline)
* Detailed table with current status of all vehicles
* Filtering by vehicle type, group, movement status, and connection status
* Data and report export capabilities

## Technical requirements

* Windows 10/11 or Windows Server 2016+
* Microsoft Power BI Desktop (current version)
* Microsoft account for Power BI Service access (optional)
* Minimum 4 GB RAM (8 GB recommended)
* Internet access for database connection

## Installation and setup

### 1. Install Power BI Desktop

1. Download Power BI Desktop from the official Microsoft website: [https://powerbi.microsoft.com/desktop/](https://powerbi.microsoft.com/desktop/)
2. Run the installer and follow its instructions.

### 2. Download the dashboard file

1.  Download the [moving\_status\_dashboard.pbix](https://raw.githubusercontent.com/SquareGPS/bi-intergrations/main/power_bi/moving_status_dashboard.pbix) file from the [bi-intergrations repository](https://github.com/SquareGPS/bi-intergrations) -> `power_bi`. You can clone the whole repository for further interactions:

    ```sh
    git clone https://github.com/SquareGPS/bi-intergrations.git
    ```
2. Open the downloaded `moving_status_dashboard.pbix` file by double-clicking or through the Power BI Desktop menu: **File → Open**.

## Database connection

### 1. Update connection parameters

1. After opening the dashboard file, go to **Transform data → Edit parameters**.
2. Update the following parameters:

* `DB_HOST` - database server address
* `DB_NAME` - database name
* `DB_USER` - username
* `DB_PASS` - password (do not save files with real credentials in shared access)
* `DB_PORT` - connection port (default 5432)

3. Click **OK** and apply the changes.

#### Connection parameter reference

<table><thead><tr><th width="189.727294921875">Lakehouse Parameter</th><th width="214.1817626953125">Power BI Setting Location</th><th>Notes</th></tr></thead><tbody><tr><td><strong>Host</strong></td><td><code>DB_HOST</code> parameter</td><td>The database server address provided in your welcome email</td></tr><tr><td><strong>Port</strong></td><td><code>DB_PORT</code> parameter</td><td>Default is 5432 for PostgreSQL</td></tr><tr><td><strong>Database name</strong></td><td><code>DB_NAME</code> parameter</td><td>Your assigned database name</td></tr><tr><td><strong>Username</strong></td><td><code>DB_USER</code> parameter</td><td>Your database username</td></tr><tr><td><strong>Password</strong></td><td><code>DB_PASS</code> parameter</td><td>Your secure database password</td></tr><tr><td><strong>SSL mode</strong></td><td>Connection Settings</td><td>Set to <strong>require</strong> in the Options dialog</td></tr><tr><td><strong>Schema</strong></td><td>Query Editor</td><td>Specify schema (<strong>raw_business_data</strong> or <strong>raw_telematics_data</strong>) in each query</td></tr></tbody></table>

### 2. Configure credentials

1. When connecting for the first time, Power BI will ask for database access credentials.
2. Select **Database account and password** as the authentication type.
3. Enter the credentials provided by your administrator.
4. Set privacy level to **Organization** or **Private**.

## Using the dashboard

After setting up the connection, you can:

1. **Refresh data** - click the **Refresh** button on the ribbon or use the keyboard shortcut Ctrl+R.
2. **Use filters** - apply filters on the right panel or directly on report elements:
   1. Filter by vehicle type
   2. Filter by groups
   3. Filter by movement/connection status
3. **Drill down into data** - click on visualization elements to view detailed information.
4. **Create bookmarks** - save specific filter settings and views for quick access.

## Data refresh configuration

### Local refresh

1. In Power BI Desktop, open the menu **Home → Refresh**.
2. To set up regular refreshes through Power BI Service, publish the report to the Power BI Service.

### Cloud refresh (Power BI Service)

1. (Optional) Publish the dashboard to Power BI Service using the **Publish** button in Power BI Desktop.
2. In the Power BI Service, go to dataset settings.
3. In the **Scheduled refresh** section, configure the refresh frequency.
4. To connect to a local or private database, you'll need to install and configure the Power BI Gateway.

## Troubleshooting

### Database connection issues

* **Connection error:** Check the correctness of credentials and connection parameters.
* **Firewall error:** Ensure your IP address is added to the allowlist for database access.
* **Gateway issues:** When using Power BI Gateway, check the status of the gateway service.

### Performance issues

* **Slow visualization loading:**
  * Reduce the number of simultaneously displayed elements
  * Check Import/DirectQuery mode in the data model
* **High memory usage:**
  * Reduce the amount of imported data by applying filters at the query level
  * Remove unnecessary columns from the data model

### Other issues

Here are some tricks that can help you fix common issues:

1. Reopen Power BI Desktop and the dashboard file
2. Check for Power BI Desktop updates
3. Reinstall Power BI Desktop if necessary
4. Clear the Power BI cache: **File → Options → Global → Clear items from cache**

## Next steps

After successfully connecting Power BI to your Private Telematics Lakehouse instance, we recommend you to:

* Explore the available data schemas by reviewing the [Schema overview](../../schema-overview/) section to better understand the data structure and relationships.
* Start with simple queries focused on specific business entities before building complex dashboards - check our [example queries](../../../example-queries/) for reference.

### **Support**

For technical questions or requests for access to the demonstration database, please contact: [support@squaregps.com](mailto:support@squaregps.com)
