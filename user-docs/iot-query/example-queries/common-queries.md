---
title: Common queries
description: Sample SQL patterns for object information, device locations, vehicle activity, driver assignments, sensor data, and geospatial zone analysis
---

# Common queries

As you connect to the database, you will be able to retrieve data via SQL queries. This section provides sample SQL queries to help you start working with the Private Telematics Lakehouse. These examples demonstrate how to access and analyze data from the **Raw data** layer, which contains raw business and telematic data with minimal transformation.

{% hint style="danger" %}
Please note: Due to the database containing a huge amount of information, please make sure to make test queries based on a limited number of values retrieved.
{% endhint %}

## Basic queries

### Retrieve basic object information

This query returns information about objects (vehicles/assets) in your system:

```sql
SELECT
    o.object_id,
    o.object_label,
    o.model,
    d.device_imei,
    g.group_label
FROM
    raw_business_data.objects o
    LEFT JOIN raw_business_data.devices d ON o.device_id = d.device_id
    LEFT JOIN raw_business_data.groups g ON o.group_id = g.group_id
WHERE
    o.is_deleted = false
ORDER BY
    o.object_label;
```

### Get latest device locations

Retrieve the most recent location data for all your devices:

```sql
SELECT
    t.device_id,
    o.object_label,
    -- Convert scaled integer coordinates back to decimal format
    t.latitude::float / 10000000 AS latitude,
    t.longitude::float / 10000000 AS longitude,
    t.speed,
    t.device_time
FROM
    raw_telematics_data.tracking_data_core t
    JOIN raw_business_data.objects o ON t.device_id = o.device_id
WHERE
    t.device_time > (CURRENT_DATE - INTERVAL '1 day')
    AND t.latitude != 0
    AND t.longitude != 0
ORDER BY
    t.device_id, t.device_time DESC;
```

{% hint style="info" %}
Coordinate values are stored as integers scaled by 10^7 for improved storage efficiency in TimescaleDB. When querying, divide by 10000000 to convert back to standard decimal format.
{% endhint %}

## Joining business and telematic data

### Vehicle activity report

This query generates a daily activity summary by joining business and telematic data:

```sql
SELECT
    o.object_label AS vehicle,
    v.vehicle_type,
    DATE(t.device_time) AS date,
    COUNT(DISTINCT DATE_PART('hour', t.device_time)) AS active_hours,
    MAX(t.speed) AS max_speed,
    AVG(t.speed) AS avg_speed
FROM
    raw_telematics_data.tracking_data_core t
    JOIN raw_business_data.objects o ON t.device_id = o.device_id
    LEFT JOIN raw_business_data.vehicles v ON o.object_id = v.object_id
WHERE
    t.device_time BETWEEN '2025-03-01' AND '2025-03-28'
GROUP BY
    o.object_label, v.vehicle_type, DATE(t.device_time)
ORDER BY
    o.object_label, DATE(t.device_time);
```

### Driver assignments and location history

Track which employees were assigned to which vehicles and their location history:

```sql
SELECT 
    o.object_label AS vehicle,
    new_row.changed_datetime AS assignment_time,
    e_new.first_name || ' ' || e_new.last_name AS new_driver_name,
    e_old.first_name || ' ' || e_old.last_name AS old_driver_name,
    new_row.address,
    new_row.latitude,
    new_row.longitude
FROM 
    raw_business_data.driver_history new_row
JOIN 
    raw_business_data.driver_history old_row
    ON new_row.changed_datetime = old_row.changed_datetime
    AND new_row.object_id = old_row.object_id
LEFT JOIN 
    raw_business_data.employees e_new ON new_row.new_employee_id = e_new.employee_id
LEFT JOIN 
    raw_business_data.employees e_old ON old_row.old_employee_id = e_old.employee_id
LEFT JOIN 
    raw_business_data.objects o ON new_row.object_id = o.object_id
ORDER BY 
    assignment_time;
```

## Analyzing sensor data

### Fuel level tracking

This query shows how to analyze fuel sensor data:

```sql
SELECT
    o.object_label AS vehicle,
    t.device_time,
    i.value::numeric AS fuel_level
FROM
    raw_telematics_data.inputs i
    JOIN raw_business_data.objects o ON i.device_id = o.device_id
    JOIN raw_telematics_data.tracking_data_core t ON
        i.device_id = t.device_id AND
        i.device_time = t.device_time
WHERE
    i.sensor_name = 'fuel'
    AND t.device_time > (CURRENT_DATE - INTERVAL '7 days')
ORDER BY
    o.object_label, t.device_time;
```

## Geospatial analysis

### Vehicles in geozones

Identify which vehicles entered specific geozones:

```sql
SELECT
    o.object_label AS vehicle,
    z.zone_label AS geozone,
    t.device_time AS entry_time
FROM
    raw_telematics_data.tracking_data_core t
    JOIN raw_business_data.objects o ON t.device_id = o.device_id
    JOIN raw_business_data.zones z ON
        -- Calculate if point is within circular zone
        -- Convert coordinates from scaled integers to decimal
        (
            CASE
                WHEN z.zone_type = 'circle' THEN
                    ST_DWithin(
                        ST_MakePoint(t.longitude::float/10000000, t.latitude::float/10000000)::geography,
                        ST_MakePoint(z.circle_center_longitude, z.circle_center_latitude)::geography,
                        z.radius
                    )
                ELSE false
            END
        )
WHERE
    t.device_time > (CURRENT_DATE - INTERVAL '1 day')
ORDER BY
    z.zone_label, o.object_label, t.device_time;
```

{% hint style="info" %}
This query uses PostGIS spatial functions. If you encounter errors, verify that PostGIS extension is enabled in your database.
{% endhint %}

## Performance optimization tips

When working with the Cloud Data Warehouse, consider these optimization techniques:

1. **Use time-based filtering**: Always include a time filter on the `device_time` or `record_added_at` columns to limit the data scanned.\
   **Good practice**:

```sql
SELECT * FROM raw_telematics_data.tracking_data_core 
WHERE device_time > (CURRENT_DATE - INTERVAL '7 days');
```

**Avoid this** (scans entire table)

```sql
SELECT * FROM raw_telematics_data.tracking_data_core;
```

2. **Leverage indexes**: The database has indexes on `(device_id, device_time)` pairs. Structure your queries to use these indexes when possible.
3. **Use joins selectively**: Join tables only when necessary and try to filter data before joining large tables.
4. **Scale integer conversion**: Remember that coordinate data is stored as scaled integers. Convert only in the final SELECT, not in WHERE clauses.
5. **Limit result sets**: Always use LIMIT for exploratory queries to avoid returning millions of rows.

```sql
SELECT * FROM raw_telematics_data.tracking_data_core 
WHERE device_time > (CURRENT_DATE - INTERVAL '1 day')
LIMIT 1000;
```

6. **Utilize hierarchical relationships**: Structure complex queries following the entity hierarchy (**dealer → client → user/device → object**) for more efficient joins and filtering.
7. **Manage connections properly**: Close database connections when they're not in use, especially in BI tools or scheduled scripts, to avoid resource locking or timeout issues.

## Next steps

These examples provide a starting point for working with your data. As you become more familiar with the schema, you can develop more complex queries to meet your specific business needs.
