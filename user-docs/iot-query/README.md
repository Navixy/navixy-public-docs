---
title: IoT Query
description: Query telematics and business data from IoT Query via PostgreSQL. Connect BI tools, run custom analytics, and build integrations with your complete dataset
---

# IoT Query

## Introduction

<figure><img src=".gitbook/assets/BA_Introducing_IoTQuery.jpg" alt=""><figcaption></figcaption></figure>

Navixy's [**IoT Query**](https://navixy.com/en/iot-query/) gives you direct access to your valuable business and telematics data in a unified, SQL-queryable environment. Built on our **Private Telematics Lakehouse (PTL)** database infrastructure, this documentation will help you connect to your data, understand what's available, and start extracting insights right away.

## Your data journey

Every organization collects vast amounts of operational data through the Navixy platform. While the platform's interface provides standard reports and visualizations, your unique business questions often require deeper, more customized analysis.

That's where **IoT Query** comes in.

We've taken your complete dataset and stored it in our PTL database infrastructure, making it directly accessible through a standard PostgreSQL connection. This opens up new possibilities for analysis, integration, and insight discovery beyond what's possible through standard platform reporting.

### What are the key benefits?

| <p><strong>Direct SQL access</strong><br><br>Query your data directly via PostgreSQL without API constraints</p>            | <p><strong>Complete data access</strong><br><br>Work with your full dataset of data available on the Navixy Platform</p>                                                                                               | <p><strong>Customization</strong><br><br>Build custom analytics, reports, and integrations for your specific scenarios</p> |
| --------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>Data integration</strong><br><br>Connect with BI and analytics tools for advanced visualization and research</p> | <p><strong>Structured data model</strong><br><br>Follow a <a href="./#how-data-is-organized">layered architecture</a>:<br><strong>Raw data</strong> → <strong>Transformations</strong> → <strong>Insights</strong></p> | <p><strong>Client isolation</strong><br><br>Secure data environment with proper access controls at all levels</p>          |
| <p><strong>Near real-time data</strong></p><p>Monitor data with minimal streaming delay</p>                                 | <p><strong>Longitudinal storage</strong></p><p>Access historical data for analysis and model training</p>                                                                                                              | <p><strong>Cross-joins</strong></p><p>Connect telematics and business data<br></p>                                         |

{% hint style="info" %}
Want to get the most out of your analytics? Enable **IoT Query** first to unlock comprehensive data insights. Contact us for activation details at [iotquery@navixy.com](mailto:iotquery@navixy.com).
{% endhint %}

### What data is available?

Your **IoT Query** contains two primary types of information:

{% columns %}
{% column %}
**Business data**

Includes your organization's structural information:

* Users and employees
* Devices and objects
* Vehicles and assignments
* Tasks, zones, and operational records
{% endcolumn %}

{% column %}
**Telematics data**

Contains the continuous stream of information from your tracking devices:

* GPS coordinates and movement data
* Sensor readings
* States
{% endcolumn %}
{% endcolumns %}

The combination of data types enables both operational reporting and deeper analytical insights.

### How data is organized?

{% columns %}
{% column %}
We've structured your data using a multi-layered architecture that balances immediate access with analytical performance. Each layer itself is sufficient for different scenarios:

1. [**Raw data layer**](iot-query/schema-overview/bronze-layer.md) (Available now) – A complete dataset, comprising telematics and business data, with minimal transformation, ready for exploration and detailed analysis
2. [**Transformation layer**](iot-query/schema-overview/transformation-layer/) (Coming soon) – Cleansed and transformed data optimized for reporting and analytics
3. **Insight layer** (Coming soon) – Business-ready data marts and aggregated metrics for specific use cases
{% endcolumn %}

{% column %}
<figure><img src=".gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="Data pipeline overview Your data flows through a comprehensive pipeline before reaching the warehouse: Business data is collected from client’s cabinet Telematics data streams directly from devices Reference metadata is loaded to enable proper data mapping and relationships Data is stored in the appropriate layer based on its processing stage"><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

### Who can benefit from this platform?

**Data analysts** will appreciate direct SQL access to build custom reports and extract insights that answer specific business questions.

**Data scientists** can leverage the complete dataset for advanced analytics, developing machine learning models and predictive capabilities.

**Developers** gain the ability to create seamless integrations between operational data and other business systems through standard database connections.

## **IoT Query** demo application

To showcase the analytical potential of your PTL database, we provide Explorer for **IoT Query** - a Streamlit-based web interface that illustrates what becomes possible when you have direct access to your complete telematics and business dataset.

This demonstration application serves multiple purposes:

* **Immediate operational value**: Provides real-time dashboards and historical reports for fleet monitoring
* **Analytical showcase**: Demonstrates four levels of capability - real-time operational intelligence, historical pattern analysis, interactive data exploration, and advanced SQL-based analytics
* **Production planning**: Helps you understand your analytical requirements before investing in production BI infrastructure

The application connects directly to your PTL database and shows how organizations can move from standard platform constraints to custom analytics solutions tailored to specific business requirements.

{% hint style="info" %}
For scalable production analytics, consider integrating dedicated BI tools like Power BI, Apache Superset, or Streamlit directly with your PTL database. If in doubt which solution fits you, see [Selecting BI tools](iot-query/connection-setup/selecting-bi-tools/).
{% endhint %}

## Support and assistance

For technical support and assistance with **IoT Query** please contact our support team at [support@navixy.com](mailto:support@navixy.com).
