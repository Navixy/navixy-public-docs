---
title: Built-in dashboards
description: Browse the 14 ready-made dashboards that ship with Dashboard Studio, covering fleet safety, driver performance, asset health, and compliance
---

# Built-in dashboards

Dashboard Studio ships with 14 ready-made dashboards covering the most frequent fleet analytics scenarios: safety and security, driver performance, asset health, and leasing compliance. Navixy builds and maintains these dashboards internally, then mirrors them to the public [navixy-iot-query-dashboard](https://github.com/Navixy/navixy-iot-query-dashboard) repository. You can browse, import, and adapt them without writing SQL from scratch.

Each dashboard includes an overview document describing its goal, target audience, and panel layout. It also ships a schema file you can import directly into your Dashboard Studio instance.

{% hint style="info" %}
Browse the full set in the repository's <a href="https://github.com/Navixy/navixy-iot-query-dashboard/tree/main/schemas" class="button primary">schemas folder</a>
{% endhint %}

## Available dashboards

All 14 dashboards read from `raw_business_data.objects`, and 10 of the 14 also depend on `processed_common_data.trips`. If trip processing isn't running in your environment, most of the set returns empty results. The Requires column lists each dashboard's other data dependencies.

In the dashboard names below, HM stands for heavy machinery and HW for hardware.

| Dashboard                                          | What it shows                                                                                    | Requires                                                  | Default period | Files                                                                                                                                                                                                                                                    |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Fleet Anomaly Monitor**                          | GPS signal loss, prolonged downtime, and abnormal geozone exits across the fleet                 | Trip processing, geofences                                | 30 days        | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/01-fleet-anomaly-monitor-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/01-fleet-anomaly-monitor.md)                         |
| **Fleet Performance Dashboard**                    | Fleet utilization, driver performance, safety events, and geozone activity in one monthly review | Trip processing, geofences, driver assignment             | 30 days        | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/02-fleet-performance-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/02-fleet-performance-dashboard.md)             |
| **Fleet Reports Dashboard**                        | Real-time connectivity status, speeding violations, device supply voltage, and average mileage   | Trip processing, PostGIS, supply-voltage sensor           | 30 days        | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/03-fleet-reports-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/03-fleet-reports-dashboard.md)                     |
| **HM Trip Operations Dashboard**                   | Heavy machinery trips split by day and night shift, with anomalous short/long trip detection     | Trip processing, vehicle records                          | 7 days         | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/04-hm-trip-operations-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/04-hm-trip-operations-dashboard.md)           |
| **Heavy Machinery – Actual Engine Operation**      | Engine hours, operational zone visits, temperature violations, and unauthorized machinery use    | RPM and temperature sensors, geofences                    | 7 days         | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/05-heavy-machinery-engine-operation-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/05-heavy-machinery-engine-operation.md)   |
| **Leasing Dashboard**                              | Document expiry tracking, harsh driving events, and engine idle time for leased-asset compliance | Insurance and license dates, ignition state, PostGIS      | 72 hours       | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/06-leasing-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/06-leasing-dashboard.md)                                 |
| **Object Status Dashboard**                        | Real-time connectivity and movement status snapshot for dispatchers                              | PostGIS, driver history, battery sensor                   | 72 hours       | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/07-object-status-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/07-object-status-dashboard.md)                     |
| **Trips Dashboard (Yesterday)**                    | Previous day's trip report: mileage, hourly activity, top vehicles, and zone activity            | Trip processing, trip zone fields                         | 1 day          | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/08-trips-dashboard-yesterday-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/08-trips-dashboard-yesterday.md)                 |
| **Vehicle Mileage Dashboard**                      | Mileage split by business hours, after hours, and weekends to flag unauthorized vehicle use      | Trip processing, departments                              | 72 hours       | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/09-vehicle-mileage-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/09-vehicle-mileage-dashboard.md)                 |
| **Safety & Security (Premium)**                    | Combined security, safety, asset condition, and cargo monitoring across four domains             | Driver performance events, door and refrigeration-unit sensors, zone tags | 24 hours       | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/10-premium-safety-security-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/10-premium-safety-security-dashboard.md) |
| **HW Status Dashboard**                            | Fleet-wide hardware health: connectivity, movement, sensor readings, and boolean sensor states   | Sensor registration and calibration                       | 72 hours       | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/11-hw-status-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/11-hw-status-dashboard.md)                             |
| **Driving Score Dashboard**                        | Composite 0–100 driving score per vehicle, calculated from violations and mileage                | Driver performance events, driver assignment              | 1 month        | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/12-driver-performance-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/12-driver-performance-dashboard.md)           |
| **Behavior Impact Dashboard**                      | Idling, aggressive driving, speeding, and high-RPM trends, compared week over week               | Driver performance events, trip zone fields               | 7 days         | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/13-behavior-impact-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/13-behavior-impact-dashboard.md)                 |
| **HW Asset Detail Dashboard**                      | Deep-dive view of a single selected asset: location, alarm state, sensors, and recent events     | Sensor registration, hourly sensor aggregates             | 24 hours       | [JSON](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/14-hw-asset-detail-dashboard-schema.json) · [Docs](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/14-hw-asset-detail-dashboard.md)                 |

### Dashboards by theme

* **Fleet health and operations:** Fleet Anomaly Monitor, Fleet Performance Dashboard, Fleet Reports Dashboard, Object Status Dashboard, HW Status Dashboard, HW Asset Detail Dashboard
* **Trips and mileage:** HM Trip Operations Dashboard, Trips Dashboard (Yesterday), Vehicle Mileage Dashboard
* **Heavy machinery:** Heavy Machinery – Actual Engine Operation
* **Leasing and compliance:** Leasing Dashboard
* **Safety and driver behavior:** Safety & Security (Premium), Driving Score Dashboard, Behavior Impact Dashboard

## Known limitations

* **The dashboard time picker doesn't change results.** Every built-in dashboard has its reporting period written into its panel SQL. Changing the range in the top-right corner has no effect. To use a different window, edit the interval in each panel's query.
* **Set a speed limit on each vehicle.** Where a vehicle record has no speed limit, these dashboards fall back to a built-in default. Fleet Performance and Fleet Reports use 120 km/h. Safety & Security and Behavior Impact use 70 km/h. Until you set per-vehicle limits, speeding counts are too low or too high depending on the dashboard.
* **All values are metric.** Distances are in kilometers, speeds in km/h, and temperatures in Celsius. There is no imperial option. Convert in the panel SQL if you need miles or Fahrenheit.
* **Day and shift boundaries follow the database server timezone, not your browser's.** The server timezone affects daily totals, day/night shift splits, and business-hours calculations.
* **Some categories are matched by exact English text.** Vehicle Mileage splits mileage across departments named exactly Drivers, Logistics, and Sales. Safety & Security identifies risk zones and depots by English words in zone labels. Rename your departments and zones to match, or adjust the query.
* **Charts show only the top results.** Most charts cap at the top 10 to 50 units. On a large fleet you see the head of each distribution, not the whole fleet.
* **Device reporting interval affects status accuracy.** Object Status and HW Status classify a device as online when its last message is under a minute old, and standby under three minutes. Devices reporting on longer intervals appear offline.
* **HW Asset Detail doesn't filter by the Asset selector.** Set the asset directly in each panel's `target` CTE.

## How to use a built-in dashboard

{% stepper %}
{% step %}
**Find a dashboard**

Open the [schemas folder](https://github.com/Navixy/navixy-iot-query-dashboard/tree/main/schemas) and review the overview document (`.md`) for the dashboard you want. It explains the dashboard's goal, target audience, and panel layout.
{% endstep %}

{% step %}
**Download the schema file**

Each dashboard has a matching `-schema.json` file. Download it from the repository.
{% endstep %}

{% step %}
**Create a blank report**

Create a new blank report first. Importing into an existing report replaces its entire content, so start from an empty one to avoid losing existing work.
{% endstep %}

{% step %}
**Import the schema**

Open the report, enter edit mode, open **Full Schema**, select **Import**, and choose the downloaded JSON file.
{% endstep %}

{% step %}
**Save**

Select **Save**. Nothing is stored until you save. Import only loads the file into the editor.
{% endstep %}

{% step %}
**Rename the menu entry**

Rename the report in your menu to match the dashboard's name. Saving preserves the existing menu label, so the imported dashboard's own title isn't applied automatically.
{% endstep %}

{% step %}
**Adapt the queries**

Built-in dashboards query IoT Query tables directly. Open each panel's SQL Query tab and confirm the table and column names match your IoT Query schema. Adjust them as needed before relying on the results.
{% endstep %}
{% endstepper %}

For details on the import/export mechanism itself, see [Creating dashboards](creating-dashboards.md#how-to-save-and-share-dashboards). For query adjustments, see [Writing SQL queries](writing-sql-queries.md).
