---
description: Raw data layer stores business entities, device telemetry, and asset records in three schemas. Browse key tables and field formats for direct SQL analysis
---

# Raw data layer

The Raw data layer contains 3 distinct data schemas, each serving different aspects of the telematics and business intelligence platform:

* [`raw_business_data`](raw-business-data.md) - containing tables, attributes, and values related to business information, such as vehicles, employees, geofences added by users, etc.
* [`raw_telematics_data`](raw-telematics-data.md) - containing tables, attributes, and values related to the telematics data transmitting from devices under monitoring, such as locations, inputs, outputs, and events.
* [BDR (Business Data Repository)](bdr.md) - containing tables for asset and inventory management, including configurable asset types, custom fields, asset relationships, and geospatial data for organizational resource tracking.

Each schema is optimized for its specific data domain and access patterns, providing comprehensive coverage of operational, telematic, and asset management needs.
