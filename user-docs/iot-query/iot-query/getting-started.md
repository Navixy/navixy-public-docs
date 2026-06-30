---
title: Getting started
description: Enable IoT Query from Admin Panel, configure your database connection, explore the data schema, and run initial telematics queries
---

# Getting started

## First steps

{% hint style="warning" %}
Before you can utilize data for comprehensive analytics and integration, you must first enable **IoT Query**. If you don't have it yet, contact us for enablement details - [iotquery@navixy.com](mailto:iotquery@navixy.com)
{% endhint %}

Once it's set up, you'll receive a welcome email containing detailed information. To begin your data journey, you will need:

* Database host and port
* Database name
* Username and password
* SSL mode settings

{% hint style="info" %}
If you are not administering your Platform accounts yourself, contact your [GPS / Telematics service provider](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/quick-start/about-service-providers) for access to **IoT Query**.
{% endhint %}

From there, your path forward is straight:

1. [Set up your connection](connection-setup/)\
   Configure your SQL client or analytics tool
2. [Explore data schemas](schema-overview/)\
   Discover the tables and relationships in your data
3. [Check SQL example queries](../example-queries/)\
   Use ready-made queries from our comprehensive recipe collection

We also recommend familiarizing yourself with [Data types and field standards](getting-started.md#data-types-and-field-standards) and [Important considerations](getting-started.md#important-considerations) before proceeding.

## Data types and field standards

**IoT Query** uses consistent data type conventions that may differ from the source systems:

* **Date and time fields** use the `TIMESTAMP` format
* **Text fields** use `CHARACTER VARYING` or `TEXT` types
* **ID fields** follow a consistent naming pattern with their table prefix (e.g., \`user\_id\`, \`device\_id\`)
* **Boolean fields** use prefixes like `is_`, `has_`, or `can_` (e.g., `is_active`, `has_passport`)

## Important considerations

While working with the **IoT Query**, be aware of these current limitations:

* **Error messaging** is minimal for SQL query issues
* **Transformation and Insight layers** are still in development and not available yet
* **Historical data** availability depends on your Platform subscription plan

For detailed information on structure and capability, see [Schema overview](schema-overview/).
