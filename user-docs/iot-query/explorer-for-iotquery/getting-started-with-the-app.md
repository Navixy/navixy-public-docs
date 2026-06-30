---
title: Getting started with the app
description: Configure Explorer for IoT Query via User Applications and verify connection. Navigate the interface to access dashboards, reports, and analytics tools
---

# Getting started with the app

This guide helps you set up access and begin using **Explorer for IoT Query** effectively. Follow these steps to connect to your data and start exploring analytical capabilities.

## User Applications setup

Access **Explorer for IoT Query** through the **Navixy User Applications** feature for seamless authentication and appropriate access to your organization's **PTL** instance.

{% hint style="warning" %}
Only Account Owners can create user applications. For detailed setup instructions, see [User applications](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications).
{% endhint %}

Setup process:

1. Navigate to **User Applications** in your Navixy platform.
2. Follow the [User Applications documentation](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications/creating-new-user-applications) to configure the new application and use these specific settings:
   1. **App URL:** `https://analytics.iotquery.navixy.com/`
   2. **Show as:** Select **Embedded**
   3. **GET parameters:** Enable **Session key** for authentication\
      The application automatically inherits your session credentials.
3. **Save** your new application.

Once configured, the application appears immediately in your platform sidebar and automatically inherits your session credentials for access to your PTL instance.

{% hint style="info" %}
Multiple users can share access to the same **IoT Query** instance for collaborative analysis.
{% endhint %}

Once the configuration is finalized, you can proceed to explore the application interface.

## Interface navigation

After a successful connection, you'll see the main **Explorer for** **IoT Query** interface with your connection details displayed prominently. The interface is organized into distinct areas that support different analytical workflows.

{% columns %}
{% column %}
The **Information about IoT Query** panel displays your connection details, while four main functional areas provide access to different analytical capabilities:

1. **Information about IoT Query panel:** Displays database connection details, credentials, and instance status for reference during analysis sessions
2. **Explore Your IoT Query:** Contains custom analysis tools including Schema & SQL editor for direct database queries and File Explorer for data management tasks
3. **Realtime Dashboards:** Provides pre-built monitoring interfaces for immediate operational insights without requiring technical configuration
4. **Historical Data Reports:** Offers time-based analysis tools with configurable periods for trend identification and strategic planning
5. **System:** Houses monitoring and configuration controls including System Health Status indicators and PTL Configuration management tools
{% endcolumn %}

{% column %}
![](../.gitbook/assets/Explorer_menu.webp)
{% endcolumn %}
{% endcolumns %}

With the interface layout understood, you can now begin your initial setup verification.

## First login checklist

Try these steps to ensure that your **Explorer for** **IoT Query** environment is properly configured and ready for analytical work:

1. **Verify connection:** Check **System Health Status** for database and streaming indicators
2. **Check data availability:** Confirm your **PTL** instance has completed initial data loading
3. **Test basic functionality:** Open any dashboard to verify data displays correctly

## Getting immediate value

Following these practices will help you achieve productive results from your first analytical sessions:

* **View live operations:** Open any **Realtime Dashboard** to see your fleet status instantly
* **Focus your analysis:** Use time filters when exploring historical data
* **Verify system performance:** Check **System Health Status** before starting analysis sessions
* **Work with your team:** Share insights using collaborative access to the same instance

## Where to go next

Based on your specific analytical needs, choose the appropriate section to continue your **Explorer for IoT Query** journey:

**For immediate operational needs:** Start with [Real-time Dashboards](real-time-dashboards.md) for live fleet monitoring

**For trend analysis:** Explore [Historical Reports](historical-reports.md) for pattern identification

**For specialized analysis:** Use [Custom Analysis & SQL Configurator](custom-analysis-sql-configurator/) when standard reports don't meet your requirements
