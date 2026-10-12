---
title: Real-time dashboards
description: "Monitor fleet operations in real time with four pre-built dashboards: object status, asset intelligence, sensor readings, and geozone activity"
---

# Real-time dashboards

Real-time dashboards transform raw telematics data streams into operational intelligence, enabling proactive fleet management through live monitoring and immediate decision-making capabilities.

## Object status dashboard

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**When to use**: Daily fleet coordination, shift handovers, and identifying vehicles requiring immediate attention.

**What data you see**: Your fleet's current operational state aggregated from the latest GPS tracking records, movement sensors, and communication timestamps.

<details>

<summary>Data processing logic</summary>

Please note - Explorer for DataHub uses its own calculation logic to identify movement and connection statuses. The dashboard processes live telematics data through a multi-step analysis:

* **Fleet status aggregation**: The system queries the most recent tracking records for each vehicle using `DISTINCT ON (device_id)` to ensure current information. Movement classification combines speed readings with time-based analysis to distinguish between actively moving vehicles, temporarily stopped vehicles, and parked assets.
* **Connection monitoring**: Vehicle connectivity status derives from communication timestamp analysis, categorizing each device based on how recently it transmitted data to your DataHub. This enables immediate identification of communication issues or device malfunctions.
* **Geographic visualization**: Raw coordinate data (stored as integers) undergoes conversion to decimal degrees for mapping display, while real-time geofence calculations determine zone presence for operational context.
* The underlying query joins business entity data (vehicle specifications, employee assignments) with current telematics readings to provide comprehensive operational context in a single view.

</details>

## Assets intelligence dashboard

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**When to use**: Investigating specific vehicles flagged in status monitoring, detailed operational planning, and comprehensive asset verification.

**What data you see**: Complete asset profiles combining organizational data incl groups, tags, departments, etc with current operational status, providing detailed context for each vehicle in your fleet.

<details>

<summary>Data processing logic</summary>

This dashboard executes complex cross-schema joins to merge:

* **Business context**: Vehicle specifications, employee assignments, and organizational hierarchies from your business data tables provide operational context for each asset.
* **Current telematics state**: Latest GPS positions, movement status, and sensor readings from tracking data streams give immediate operational visibility.
* **Enhanced data integration**: The system dynamically generates mapping links using current coordinates and retrieves battery levels from sensor inputs when available. When sensor calibration data is missing, raw values display without modification to maintain data transparency.

{% hint style="info" %}
All timestamp data converts to UTC for consistent display regardless of vehicle geographic location, ensuring accurate operational coordination across different time zones.
{% endhint %}

</details>

## Sensor readings dashboard

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**When to use**: Preventive maintenance planning, fuel management monitoring, and identifying potential equipment issues before they impact operations.

**What data you see**: Real-time sensor measurements from your fleet's monitoring equipment, processed through calibration algorithms to provide accurate operational metrics.

<details>

<summary>Data processing logic</summary>

* **Multi-sensor aggregation**: The system queries `processed_common_data.latest_calibrated_sensors` to retrieve the most recent measurements across different sensor types simultaneously. This includes fuel levels, temperature readings, battery voltage, and operational state indicators.
* **Calibration processing**: Raw sensor values undergo calibration factor application when available. The system applies sensor-specific formulas to convert raw readings into meaningful units (percentages for fuel, Celsius for temperature, volts for electrical systems).
* **Quality assurance**: Basic validation filters obviously invalid measurements while preserving data transparency. When calibration data is unavailable or invalid, the system displays raw sensor values without modification, enabling operational teams to make informed decisions based on available data.

The dashboard uses time-based queries with sensor-specific distinctness to ensure you see the latest reading from each sensor on each vehicle, ordered by timestamp for current operational relevance.

</details>

## Geozones report

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

**When to use**: Tracking vehicle visits to specific locations, analyzing route compliance, and monitoring operational area coverage.

**What data you see**: Historical and current records of vehicle entries and exits from defined geographic zones, with precise timestamps and location coordinates.

<details>

<summary>Data processing logic</summary>

* **Zone geometry processing**: The system handles different geofence types (circles, polygons, corridors) through PostGIS geographic calculations. Circle zones use center points with radius buffers, while polygon zones create complex geographic boundaries from coordinate arrays.
* **Visit detection**: Real-time geographic analysis compares vehicle coordinates with zone boundaries using `ST_DWithin` calculations to determine zone entries and exits. The system tracks visit duration by calculating time differences between entry and exit events.
* **Location context**: Raw coordinate data converts to decimal degrees for address resolution and mapping integration, while zone labels provide business context for each geographic area.

Visit records combine zone geometry data with vehicle tracking history to create comprehensive location intelligence, enabling analysis of route patterns, compliance monitoring, and operational area utilization.

</details>

## Next steps

When real-time monitoring reveals patterns requiring deeper investigation, progress to [Historical Data Reports](historical-reports.md) to analyze trends over extended periods and identify optimization opportunities for strategic fleet management decisions.
