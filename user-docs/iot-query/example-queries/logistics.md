---
description: SQL query templates for logistics and transportation analytics, covering route optimization, cargo monitoring, fleet utilization, and delivery performance
---

# Logistics

{% hint style="warning" %}
Enable **IoT Query** before utilizing data for building comprehensive analytics. If you don't have it yet, contact us for activation details - [iotquery@navixy.com](mailto:iotquery@navixy.com)
{% endhint %}

Logistics is a complex ecosystem involving the coordination of transport, warehouse operations, inventory, and delivery execution. Integrating telematics into logistics processes allows companies to collect real-time data on vehicles, drivers, routes, and cargo conditions, which significantly improves decision-making and operational efficiency.

Navixy **IoT Query**, with its robust data ingestion and time-series analytics capabilities, supports the digital transformation of logistics operations by enabling deep visibility into each lifecycle stage. Its robust telematics ingestion capabilities, provides comprehensive visibility into these operations. Real-time GPS data, sensor data diagnostics, geofencing, and sensor analytics allow logistics operators to digitize workflows, automate controls, and make informed decisions.

| Lifecycle Phase             | Goals                                                                     | Covered Use Cases / Recipes                                                                                                  |
| --------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Route management**        | Optimize vehicle routing, ensure efficient dispatching, and reduce delays | Trip Counts per Day Mileage Count per Vehicle per Day (Last 7 Days)                                                          |
| **Cargo Monitoring**        | Ensure proper transport conditions for sensitive goods                    | Temperature (and Humidity) Violation Events in the Last 7 Days                                                               |
| **Vehicle Operation**       | Track fleet utilization, ensure maintenance, and reduce downtime          | Engine Hours Summary per Vehicle / Driver / Day (Last 7 Days) Vehicle Downtime Analysis Asset Tracking Without Movement      |
| **Route Security & Safety** | Detect misuse, unauthorized activity, and safety violations               | Route Deviation Detection- Unauthorized Stops (Last 24 Hours) Off-Hour Usage Detection                                       |
| **Compliance Management**   | Monitor driver behavior, enforce policies and operational compliance      | Engine Hours Summary per Vehicle / Driver / Day (Last 7 Days) Off-Hour Usage Detection                                       |
| **Post-Delivery Analysis**  | Evaluate operational efficiency and historical performance                | Vehicle Event Log Report Mileage Count per Vehicle per Day (Last 7 Days) Trip Counts per Day Asset Tracking Without Movement |

## **Asset Tracking Without Movement** <a href="#asset-tracking-without-movement" id="asset-tracking-without-movement"></a>

This case identifies assets (e.g., vehicles or trailers) that have not changed their GPS compare the **minimum and maximum coordinates** during the period. If both values fall within a very narrow range (a tolerance threshold, e.g., ±0.01 degrees), we flag the asset as non-moving. The query also joins with the objects and vehicles tables in raw\_business\_data to retrieve meaningful asset labels for the result output.

{% code expandable="true" %}
```sql
WITH gps_bounds AS (
    SELECT
        td.device_id,
        MIN(td.latitude) AS min_lat,
        MAX(td.latitude) AS max_lat,
        MIN(td.longitude) AS min_lon,
        MAX(td.longitude) AS max_lon,
        COUNT(*) AS location_records
    FROM raw_telematics_data.tracking_data_core td
    WHERE td.device_time >= now() - interval '48 hours'
    GROUP BY td.device_id
),
stationary_devices AS (
    SELECT
        device_id
    FROM gps_bounds
    WHERE location_records > 10 -- exclude devices with very sparse data
	AND((max_lat - min_lat) <= 2000 -- ~10 meters
		OR
      	(max_lon - min_lon) <= 1000)  -- ~10 meters
    		)
SELECT
    v.vehicle_id,
    v.vehicle_label,
    o.object_id,
    o.object_label,
    sd.device_id,
    gb.min_lat / 1e7 AS latitude,
    gb.min_lon / 1e7 AS longitude,
    gb.location_records
FROM stationary_devices sd
JOIN gps_bounds gb ON sd.device_id = gb.device_id
JOIN raw_business_data.objects o ON o.device_id = sd.device_id
LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
ORDER BY gb.location_records DESC;
```
{% endcode %}

## **Vehicle Downtime Analysis** <a href="#vehicle-downtime-analysis" id="vehicle-downtime-analysis"></a>

This case focuses on analyzing how long vehicles are non-operational due to maintenance, breakdowns, or inactivity. Downtime metrics are crucial for logistics operations to monitor fleet health, reduce idle time, and improve overall utilization and scheduling efficiency.

The core of the downtime analysis lies in leveraging the vehicle\_service\_tasks table from raw\_business\_data, which logs both **planned and unplanned maintenance events**. Each task contains a start\_date and an end\_date, representing the downtime period. By filtering for **completed service tasks**, we can compute the exact duration each vehicle was out of operation.

The query calculates total downtime per vehicle by summing up the durations of all its service tasks (in hours). It also allows breakdown by planned vs unplanned maintenance by using the is\_unplanned flag. To make the results more actionable, it joins with the vehicles table to include vehicle labels, registration numbers, and model information.

{% code expandable="true" %}
```sql
WITH downtime_durations AS (
    SELECT
        vst.vehicle_id,
        vst.is_unplanned,
        vst.start_date,
        vst.end_date,
        EXTRACT(EPOCH FROM (vst.end_date - vst.start_date))/3600 AS downtime_hours
    FROM raw_business_data.vehicle_service_tasks vst
    WHERE vst.status = 'done'
      AND vst.start_date IS NOT NULL
      AND vst.end_date IS NOT NULL
)
SELECT
    v.vehicle_id,
    v.vehicle_label,
    v.registration_number,
    v.model,
    COUNT(dd.*) AS total_service_events,
    SUM(dd.downtime_hours) AS total_downtime_hours,
    SUM(dd.downtime_hours) FILTER (WHERE dd.is_unplanned = TRUE) AS unplanned_downtime_hours,
    SUM(dd.downtime_hours) FILTER (WHERE dd.is_unplanned = FALSE) AS planned_downtime_hours
FROM downtime_durations dd
JOIN raw_business_data.vehicles v ON v.vehicle_id = dd.vehicle_id
GROUP BY v.vehicle_id, v.vehicle_label, v.registration_number, v.model
ORDER BY total_downtime_hours DESC;
```
{% endcode %}

## **Route Deviation Detection** <a href="#route-deviation-detection" id="route-deviation-detection"></a>

This case identifies instances where vehicles deviate from their assigned or expected routes — particularly geofenced zones or delivery corridors. Tracking such deviations helps in ensuring route compliance, reducing delays, detecting risky driving behavior, and maintaining delivery SLAs.

This logic compares the vehicle’s actual GPS positions from tracking\_data\_core (in the raw\_telematics\_data schema) against predefined geographic zones from the zones table in raw\_business\_data. These zones represent assigned routes or route segments. Using geometric comparisons via ST\_DWithin, we determine whether a point is inside or outside the buffered route area.

The query joins each GPS position with every known route zone using a spatial **CROSS JOIN**, then applies ST\_DWithin() to check if the vehicle was within the permitted corridor. We isolate the rows where the vehicle was **outside all geofenced routes** and flag them as deviations. The final output lists these deviations, including the device, timestamp, vehicle label, and how far the point was from the nearest zone center.

{% code expandable="true" %}
```sql
WITH positions AS (
    SELECT
        td.device_id,
        td.device_time,
        o.object_id,
        o.object_label,
        z.zone_id,
        z.zone_label,
        ST_SetSRID(ST_MakePoint(td.longitude / 1e7, td.latitude / 1e7), 4326)::geography AS gps_point,
        ST_Buffer(
            ST_SetSRID(ST_MakePoint(z.circle_center_longitude, z.circle_center_latitude), 4326)::geography,
            z.radius
        ) AS route_buffer
    FROM raw_telematics_data.tracking_data_core td
    JOIN raw_business_data.objects o ON td.device_id = o.device_id
    CROSS JOIN raw_business_data.zones z
    WHERE td.device_time >= now() - interval '2 days'
),
evaluated AS (
    SELECT
        device_id,
        device_time,
        object_id,
        object_label,
        zone_id,
        zone_label,
        NOT ST_DWithin(gps_point, route_buffer, 0) AS is_deviation,
        ST_Distance(gps_point, route_buffer) AS deviation_distance_meters
    FROM positions
),
deviations_only AS (
    SELECT *
    FROM evaluated
    WHERE is_deviation = true
)
SELECT
    device_id,
    object_label,
    zone_label,
    device_time,
    deviation_distance_meters
FROM deviations_only
ORDER BY device_time DESC;
```
{% endcode %}

## **Engine Hours Summary per Vehicle / Driver / Day (Last 7 Days)** <a href="#engine-hours-summary-per-vehicle-driver-day-last-7-days" id="engine-hours-summary-per-vehicle-driver-day-last-7-days"></a>

This case measures how long engines were active for each vehicle on a daily basis, allowing fleet managers to track **utilization**, identify **overuse or underuse**, and correlate activity with driver assignments. When tied to drivers, it also supports **labor hour validation** and **performance analysis**.

The states table in raw\_telematics\_data records **time-series engine state indicators**, typically with a state\_name like 'ignition' and a value of 1 (on) or 0 (off). To calculate engine hours, we find all timestamped transitions for each device and compute durations where the engine was on (1).

To tie engine activity to both **vehicles and drivers**, we use the objects, vehicles, and driver\_history tables from raw\_business\_data. We associate each state record to the current driver on that object (via driver assignment history) and to the corresponding vehicle. We then group the data by day, vehicle, and driver, summing total active engine time (in hours).

{% code expandable="true" %}
```sql
WITH inputs_core AS (
   SELECT
       i.device_id,
       i.device_time,
       i.event_id,
       i.record_added_at,
       i.sensor_name,
       i.value
   FROM raw_telematics_data.inputs i
   WHERE i.device_time >= now() - interval '7 days'
),
clear_inputs AS (
   SELECT
       i.device_id,
       i.device_time,
       i.event_id,
       i.record_added_at,
       i.sensor_name,
       i.value,
       LAG(i.device_time) OVER (PARTITION BY i.device_id ORDER BY i.device_time) AS prev_time,
       LAG(i.value::int) OVER (PARTITION BY i.device_id ORDER BY i.device_time) AS prev_status
   FROM inputs_core i
   LEFT JOIN raw_business_data.sensor_description sd
       ON sd.input_label = i.sensor_name
      AND sd.device_id = i.device_id
   WHERE sd.sensor_type = 'engine'
     AND i.value = '1'
),
engine_on_periods AS (
   SELECT
       device_id,
       prev_time AS engine_on_time,
       device_time AS engine_off_time,
       device_time::date AS activity_day,
       EXTRACT(EPOCH FROM (device_time - prev_time)) / 3600 AS engine_hours
   FROM clear_inputs
   WHERE value::int = 0 AND prev_status = 1  -- transitions from ON to OFF
),
enriched_with_objects AS (
   SELECT
       eop.*,
       o.object_id,
       o.object_label,
       v.vehicle_id,
       v.vehicle_label,
       v.registration_number
   FROM engine_on_periods eop
   JOIN raw_business_data.objects o ON o.device_id = eop.device_id
   LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
),
assigned_drivers AS (
   SELECT
       ewo.*,
       dh.new_employee_id as employee_id
   FROM enriched_with_objects ewo
   LEFT JOIN LATERAL (
       SELECT d.*
       FROM raw_business_data.driver_history d
       WHERE d.object_id = ewo.object_id
         AND d.changed_datetime <= ewo.engine_on_time
       ORDER BY d.changed_datetime DESC
       LIMIT 1
   ) dh ON true
)
SELECT
   activity_day,
   vehicle_label,
   registration_number,
   object_label,
   e.first_name || ' ' || e.last_name AS driver_name,
   SUM(engine_hours) AS total_engine_hours
FROM assigned_drivers ad
LEFT JOIN raw_business_data.employees e ON ad.employee_id = e.employee_id
GROUP BY activity_day, vehicle_label, registration_number, object_label, driver_name
ORDER BY activity_day DESC, vehicle_label;

```
{% endcode %}

## **Temperature (and Humidity) Violation Events in the Last 7 Days** <a href="#temperature-and-humidity-violation-events-in-the-last-7-days" id="temperature-and-humidity-violation-events-in-the-last-7-days"></a>

This case identifies sensor readings — such as **temperature or humidity** — that exceed critical thresholds during transport. Monitoring such violations is vital for industries transporting perishable goods (e.g., food, pharmaceuticals) to ensure compliance with cold chain requirements and to prevent spoilage.

This query extracts **sensor input data** from the inputs table in the raw\_telematics\_data schema. Each row represents a sensor reading (e.g., temperature, humidity) recorded at a specific timestamp by a device. We filter these records to include only those from the **last 7 days**.

The main filtering logic is based on **sensor name patterns** and a comparison of their **numeric values against thresholds** (e.g., >25°C for temperature, >80% for humidity). Because value is stored as text, we cast it to numeric before applying the threshold conditions. To enrich the results, we join with the objects table to retrieve vehicle or asset labels, which improves interpretability for fleet managers.

{% code expandable="true" %}
```sql
WITH recent_sensor_data AS (
   SELECT
       i.device_id,
       i.device_time,
       i.sensor_name,
       i.value::float AS value
   FROM raw_telematics_data.inputs i
   WHERE i.device_time >= now() - interval '1 hour'
),
sensor_meta AS (
   SELECT
       sd.device_id,
       sd.input_label,
       sd.sensor_id,
       sd.sensor_type,
       sd.calibration_data
   FROM raw_business_data.sensor_description sd
),
joined_data AS (
   SELECT
       rsd.*,
       sm.sensor_id,
       sm.sensor_type,
       sm.calibration_data
   FROM recent_sensor_data rsd
   LEFT JOIN sensor_meta sm
       ON rsd.device_id = sm.device_id
      AND rsd.sensor_name = sm.input_label
),
calibrated_data AS (
   SELECT
       jd.device_id,
       jd.device_time,
       jd.sensor_name,
       jd.value,
       jd.sensor_id,
       jd.sensor_type,
       CASE
           WHEN cd_low.cal_value IS NOT NULL AND cd_high.cal_value IS NOT NULL THEN
               CASE
                   WHEN cd_high.cal_value = cd_low.cal_value THEN cd_low.cal_volume
                   ELSE cd_low.cal_volume +
                       ((jd.value - cd_low.cal_value) / NULLIF(cd_high.cal_value - cd_low.cal_value, 0))
                       * (cd_high.cal_volume - cd_low.cal_volume)
               END
           ELSE jd.value
       END AS calibrated_value
   FROM joined_data jd
   LEFT JOIN LATERAL (
       SELECT
           (p->>'in')::float  AS cal_value,
           (p->>'out')::float AS cal_volume
       FROM jsonb_array_elements(jd.calibration_data) AS p
       WHERE (p->>'in')::float <= jd.value
       ORDER BY (p->>'in')::float DESC
       LIMIT 1
   ) cd_low ON TRUE
   LEFT JOIN LATERAL (
       SELECT
           (p->>'in')::float  AS cal_value,
           (p->>'out')::float AS cal_volume
       FROM jsonb_array_elements(jd.calibration_data) AS p
       WHERE (p->>'in')::float >= jd.value
       ORDER BY (p->>'in')::float ASC
       LIMIT 1
   ) cd_high ON TRUE
),
violations AS (
   SELECT
       cd.device_id,
       cd.device_time,
       cd.sensor_name,
       cd.calibrated_value,
       CASE
           WHEN cd.sensor_type = 'temperature' AND cd.calibrated_value > 25 THEN 'High Temperature'
           WHEN cd.sensor_type = 'temperature' AND cd.calibrated_value < 0 THEN 'Low Temperature'
           WHEN cd.sensor_type = 'humidity' AND cd.calibrated_value > 80 THEN 'High Humidity'
           ELSE NULL
       END AS violation_type
   FROM calibrated_data cd
   WHERE cd.sensor_type IN ('temperature', 'humidity')
)
SELECT
   v.vehicle_label,
   o.object_label,
   v.registration_number,
   v.model,
   vio.device_time,
   vio.sensor_name,
   vio.calibrated_value,
   vio.violation_type
FROM violations vio
JOIN raw_business_data.objects o
  ON o.device_id = vio.device_id
LEFT JOIN raw_business_data.vehicles v
  ON v.object_id = o.object_id
ORDER BY vio.device_time DESC;

```
{% endcode %}

## **Unauthorized Stops (Last 24 Hours)** <a href="#unauthorized-stops-last-24-hours" id="unauthorized-stops-last-24-hours"></a>

This case identifies **unauthorized or unplanned stops** made by vehicles within the past 24 hours. It helps in detecting potential violations of delivery routes, unauthorized breaks, or idle time that may affect fuel efficiency and SLA performance.

The query analyzes **location points with low or zero speed** using the The query uses the tracking\_data\_core table from raw\_telematics\_data to extract time-series location data and speed. A stop is detected when **speed drops below 3 km/h** for a duration of **more than 2 minutes**. Using LAG and LEAD functions, the query segments these low-speed periods to determine stop start and end timestamps.

To detect **unauthorized stops**, it filters out locations that fall within known **geofenced zones** (zones table) using PostGIS's ST\_DWithin. Only stops **outside of any zone buffer** are reported. The result includes vehicle ID, object label, registration, timestamps, duration, and coordinates for each stop.

{% code expandable="true" %}
```sql
WITH speed_data AS (
    SELECT
        td.device_id,
        td.device_time,
        td.speed / 100.0 AS speed_kph,
        td.latitude / 1e7 AS lat,
        td.longitude / 1e7 AS lon
    FROM raw_telematics_data.tracking_data_core td
    WHERE td.device_time >= now() - interval '1 day'
),
low_speed_points AS (
    SELECT
        *,
        LAG(device_time) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_time,
        LAG(speed_kph) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_speed
    FROM speed_data
),
stops_marked AS (
    SELECT *,
        CASE 
            WHEN speed_kph < 3 AND (prev_speed >= 3 OR prev_speed IS NULL) THEN 1 
            ELSE 0 
        END AS stop_start,
        CASE 
            WHEN speed_kph >= 3 AND prev_speed < 3 THEN 1 
            ELSE 0 
        END AS stop_end
    FROM low_speed_points
),
stop_segments AS (
    SELECT
        device_id,
        device_time AS stop_start_time,
        LEAD(device_time) OVER (PARTITION BY device_id ORDER BY device_time) AS stop_end_time,
        lat,
        lon
    FROM stops_marked
    WHERE stop_start = 1
),
unauthorized_stops AS (
    SELECT
        ss.*,
        EXTRACT(EPOCH FROM (ss.stop_end_time - ss.stop_start_time))/60 AS stop_duration_min
    FROM stop_segments ss
    LEFT JOIN raw_business_data.zones z ON
        ST_DWithin(
            ST_SetSRID(ST_MakePoint(ss.lon, ss.lat), 4326)::geography,
            ST_SetSRID(ST_MakePoint(z.circle_center_longitude, z.circle_center_latitude), 4326)::geography,
            z.radius
        )
    WHERE z.zone_id IS NULL  -- not inside any known zone
      AND EXTRACT(EPOCH FROM (ss.stop_end_time - ss.stop_start_time)) > 120  -- minimum 2 minutes
),
with_metadata AS (
    SELECT
        us.*,
        o.object_label,
        v.vehicle_label,
        v.registration_number
    FROM unauthorized_stops us
    JOIN raw_business_data.objects o ON o.device_id = us.device_id
    LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
)
SELECT
    vehicle_label,
    registration_number,
    object_label,
    stop_start_time,
    stop_end_time,
    ROUND(stop_duration_min, 1) AS stop_duration_minutes,
    ROUND(lat, 6) AS latitude,
    ROUND(lon, 6) AS longitude
FROM with_metadata
ORDER BY stop_start_time DESC;
```
{% endcode %}

## **Off-Hour Usage Detection** <a href="#off-hour-usage-detection" id="off-hour-usage-detection"></a>

This case identifies instances where vehicles are operated **outside normal business hours** — defined here as **Monday through Friday, 09:00–18:00**. Such detections are essential for flagging **unauthorized use**, identifying potential **vehicle misuse**, and improving **asset security**.

The logic is built on the tracking\_data\_core table from raw\_telematics\_data, which logs timestamped GPS events per device. We derive local **day of week** and **hour of use** from each device\_time entry and filter for records **outside the defined business window** (i.e., before 9 AM, after 6 PM, or any time on weekends).

To provide clarity, we enrich the GPS data with object and vehicle metadata from raw\_business\_data (e.g., vehicle label, registration, object ID). For more meaningful summaries, we optionally aggregate the usage to count **how many off-hour events** occurred per vehicle and when they happened. This can help in identifying patterns or repeat offenders.

{% code expandable="true" %}
```sql
WITH gps_events AS (
    SELECT
        td.device_id,
        td.device_time,
        EXTRACT(DOW FROM td.device_time) AS day_of_week, -- 0=Sunday, 6=Saturday
        EXTRACT(HOUR FROM td.device_time) AS hour_of_day,
        td.latitude / 1e7 AS latitude,
        td.longitude / 1e7 AS longitude
    FROM raw_telematics_data.tracking_data_core td
    WHERE td.device_time >= now() - interval '7 days'
),
off_hour_events AS (
    SELECT
        ge.*
    FROM gps_events ge
    WHERE 
        day_of_week IN (0, 6)  -- Saturday or Sunday
        OR hour_of_day < 9 
        OR hour_of_day >= 18
),
with_metadata AS (
    SELECT
        o.object_label,
        v.vehicle_label,
        v.registration_number,
        e.first_name || ' ' || e.last_name AS driver_name,
        o.device_id,
        o.object_id,
        o.create_datetime,
        ge.device_time,
        ge.latitude,
        ge.longitude,
        ge.day_of_week,
        ge.hour_of_day
    FROM off_hour_events ge
    JOIN raw_business_data.objects o ON ge.device_id = o.device_id
    LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
    LEFT JOIN raw_business_data.driver_history dh ON dh.object_id = o.object_id 
        AND dh.changed_datetime <= ge.device_time
    LEFT JOIN raw_business_data.employees e ON e.employee_id = dh.new_employee_id
)
SELECT
    object_label,
    vehicle_label,
    registration_number,
    driver_name,
    device_time,
    TO_CHAR(device_time, 'Day') AS weekday,
    TO_CHAR(device_time, 'HH24:MI') AS time_of_event,
    ROUND(latitude::numeric, 6) AS lat,
    ROUND(longitude::numeric, 6) AS lon
FROM with_metadata
ORDER BY device_time DESC;
```
{% endcode %}

## **Trip counts per day** <a href="#trip-counts-per-day" id="trip-counts-per-day"></a>

This case measures **how many trips** each vehicle completes daily and how far they travel, helping logistics teams assess **vehicle usage**, optimize routes, and detect anomalies like incomplete trips or unreported usage.

To define a **trip**, we use a change in **vehicle movement state** — i.e., transition from stopped to moving and back to stopped. Using the speed values from the tracking\_data\_core table, the query segments the data based on these transitions. A trip is identified as a **continuous movement period** where the speed stays above a threshold (e.g., >5 km/h).

Each trip includes:

* A **start timestamp and location** (first moving point)
* An **end timestamp and location** (last moving point before stopping)
* The **Haversine distance** between start and end locations

We calculate the trip count and total distance per day per vehicle, optionally enriched with vehicle labels from the vehicles table.

{% code expandable="true" %}
```sql
WITH base_points AS (
    SELECT
        td.device_id,
        td.device_time,
        td.latitude / 1e7 AS lat,
        td.longitude / 1e7 AS lon,
        td.speed / 100.0 AS speed_kph,
        LEAD(td.speed / 100.0) OVER (PARTITION BY td.device_id ORDER BY td.device_time) AS next_speed,
        LAG(td.speed / 100.0) OVER (PARTITION BY td.device_id ORDER BY td.device_time) AS prev_speed
    FROM raw_telematics_data.tracking_data_core td
    WHERE td.device_time >= now() - interval '7 days'
),
trip_segments AS (
    SELECT
        *,
        CASE 
            WHEN speed_kph >= 5 AND (prev_speed < 5 OR prev_speed IS NULL) THEN 'start'
            WHEN speed_kph < 5 AND prev_speed >= 5 THEN 'end'
        END AS trip_marker
    FROM base_points
),
trip_points AS (
    SELECT
        device_id,
        device_time AS trip_start_time,
        lat AS start_lat,
        lon AS start_lon,
        LEAD(device_time) OVER (PARTITION BY device_id ORDER BY device_time) AS trip_end_time,
        LEAD(lat) OVER (PARTITION BY device_id ORDER BY device_time) AS end_lat,
        LEAD(lon) OVER (PARTITION BY device_id ORDER BY device_time) AS end_lon
    FROM trip_segments
    WHERE trip_marker = 'start'
),
trip_metrics AS (
    SELECT
        tp.device_id,
        tp.trip_start_time,
        tp.trip_end_time,
        tp.start_lat,
        tp.start_lon,
        tp.end_lat,
        tp.end_lon,
        tp.trip_start_time::date AS trip_day,
        -- Approximate distance using Haversine formula (in km)
        111 * SQRT(POWER(tp.end_lat - tp.start_lat, 2) + POWER((tp.end_lon - tp.start_lon) * COS(RADIANS(tp.start_lat)), 2))::numeric AS distance_km
    FROM trip_points tp
    WHERE tp.trip_end_time IS NOT NULL
)
SELECT
    v.vehicle_label,
    v.registration_number,
    tm.device_id,
    tm.trip_day,
    COUNT(*) AS trip_count,
    ROUND(SUM(tm.distance_km), 2) AS total_distance_km
FROM trip_metrics tm
JOIN raw_business_data.objects o ON o.device_id = tm.device_id
LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
GROUP BY v.vehicle_label, v.registration_number, tm.device_id, tm.trip_day
ORDER BY tm.trip_day DESC, v.vehicle_label;
```
{% endcode %}

## **Mileage Count per Vehicle per Day (Last 7 Days)** <a href="#mileage-count-per-vehicle-per-day-last-7-days" id="mileage-count-per-vehicle-per-day-last-7-days"></a>

This case calculates the **daily mileage** (in kilometers) for each vehicle over the last 7 days. It is fundamental for tracking **vehicle utilization**, monitoring **fuel efficiency**, planning **maintenance**, and detecting underuse or overuse.

We extract all GPS records from tracking\_data\_core for the past 7 days. Each GPS point has a timestamp, latitude, and longitude. For each vehicle and each day, we:

1. **Sort GPS points chronologically** per device.
2. **Calculate distance between consecutive points** using the Haversine formula.
3. **Sum the distances per day per device** to get total mileage.

This approach provides high accuracy without relying on external odometer sensors. Optionally, the query joins with objects and vehicles to enrich results with asset metadata.

{% code expandable="true" %}
```sql
WITH gps_points AS (
    SELECT
        td.device_id,
        td.device_time,
        td.device_time::date AS trip_day,
        td.latitude / 1e7 AS lat,
        td.longitude / 1e7 AS lon,
        LAG(td.latitude / 1e7) OVER (PARTITION BY td.device_id, td.device_time::date ORDER BY td.device_time) AS prev_lat,
        LAG(td.longitude / 1e7) OVER (PARTITION BY td.device_id, td.device_time::date ORDER BY td.device_time) AS prev_lon
    FROM raw_telematics_data.tracking_data_core td
    WHERE td.device_time >= now() - interval '7 days'
),
distances AS (
    SELECT
        device_id,
        trip_day,
        -- Haversine formula approximation in km
        111 * SQRT(POWER(lat - prev_lat, 2) + POWER((lon - prev_lon) * COS(RADIANS(lat)), 2)) AS segment_distance_km
    FROM gps_points
    WHERE prev_lat IS NOT NULL AND prev_lon IS NOT NULL
      AND ABS(lat - prev_lat) < 1 AND ABS(lon - prev_lon) < 1 -- exclude outliers
)
SELECT
    v.vehicle_label,
    v.registration_number,
    o.object_label,
    d.device_id,
    d.trip_day,
    ROUND(SUM(d.segment_distance_km)::numeric, 2) AS mileage_km
FROM distances d
JOIN raw_business_data.objects o ON o.device_id = d.device_id
LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
GROUP BY v.vehicle_label, v.registration_number, o.object_label, d.device_id, d.trip_day
ORDER BY d.trip_day DESC, v.vehicle_label;
```
{% endcode %}

## **Vehicle Event Log Report** <a href="#vehicle-event-log-report" id="vehicle-event-log-report"></a>

This case provides a comprehensive report of all **vehicle-related events** (e.g., ignition, door open, harsh braking, etc.) across the fleet. It includes **event type**, **timestamp**, and **vehicle context**, allowing operations teams to audit behavior, track abnormal activity, or drive alerts and analytics.

The primary source is the states table from the raw\_telematics\_data schema. Each row includes: device\_id (source of the event), device\_time (timestamp), state\_name (event label), and value (status or measurement).

To create a usable report:

1. We extract all records from the last 7 days.
2. Group them by **event type**, **vehicle**, and **date** to provide a **count of how often each event occurred** and **when it occurred**.
3. Enrich the results with vehicle metadata (vehicle\_label, registration\_number, object\_label) via objects and vehicles.

This gives a **daily event timeline** across the fleet - essential for diagnostics, behavioral analysis, and proactive maintenance.

{% code expandable="true" %}
```sql
WITH raw_events AS (
    SELECT
        s.device_id,
        s.device_time,
        s.device_time::date AS event_day,
        s.state_name,
        s.value
    FROM raw_telematics_data.states s
    WHERE s.device_time >= now() - interval '7 days'
),
with_vehicle_info AS (
    SELECT
        re.device_id,
        re.event_day,
        re.device_time,
        re.state_name,
        re.value,
        v.vehicle_label,
        v.registration_number,
        o.object_label
    FROM raw_events re
    JOIN raw_business_data.objects o ON o.device_id = re.device_id
    LEFT JOIN raw_business_data.vehicles v ON v.object_id = o.object_id
),
event_summary AS (
    SELECT
        event_day,
        state_name,
        vehicle_label,
        registration_number,
        object_label,
        COUNT(*) AS event_count,
        MIN(device_time) AS first_occurred,
        MAX(device_time) AS last_occurred
    FROM with_vehicle_info
    GROUP BY event_day, state_name, vehicle_label, registration_number, object_label
)
SELECT
    event_day,
    vehicle_label,
    registration_number,
    object_label,
    state_name AS event_type,
    event_count,
    first_occurred,
    last_occurred
FROM event_summary
ORDER BY event_day DESC, vehicle_label, state_name;
```
{% endcode %}
