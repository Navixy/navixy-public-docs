---
title: Where and how is data stored?
description: "MySQL database stores tracking and business data. Images stored in API-server subdirectories. Two databases: tracking and google"
---

# Where and how is data stored?

## Where is the data stored?

All tracking, telematics and business data is stored in a MySQL database deployed on the server where Navixy application is running or on a dedicated server.

In addition, all images uploaded in the web interface (branding, avatars, submitted photos in forms, etc.) are stored in the API-server subdirectories on the application server.

## How is the data stored in the database?

All the acquired data is organized into two databases:

* tracking - contains points received from trackers (coordinates, time, satellites, direction, data on inputs and outputs, etc.).
* google - contains all created entities, user and device settings, rules, reports, sensor and counter data, other business data, and anything not directly related to tracking points received from devices.

The detailed table structure can be checked in the platform build by examining the deployment scripts in the `/db` subdirectory.

## How can I export data from the database?

If you need to export data, it can be done using the common `SELECT` SQL statement. Due to the large number of tables and the variety of data, we recommend contacting the Technical Solutions department for advice on where to take data from and what SQL queries to use.
