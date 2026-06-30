---
description: Vehicle trip records derived from raw telematics data. Includes start and end times, distance, speed statistics, and zone detection
---

# Trips

The Trips transformation processes raw telematics data into discrete vehicle trip records. Each row in `processed_common_data.trips` represents one complete trip: where it started and ended, how long it took, how far the vehicle traveled, and which zones it departed from or arrived at.

The built-in transformation runs every 8 hours and covers a rolling 12-hour window of raw data. At each run, previously calculated records for that window are replaced with a fresh calculation, so the table always reflects the latest available data.

{% hint style="info" %}
* Data in this table may be up to 8 hours old, reflecting the most recently completed run.
* All timestamps are stored in UTC.
{% endhint %}

## Output table: processed\_common\_data.trips

Each row represents one validated vehicle trip. The table is keyed on `device_id` and `trip_start_time`.

<table><thead><tr><th width="215">Field</th><th width="115">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>device_id</code></td><td>integer</td><td>Device identifier. To get the object label, join to <code>raw_business_data.objects</code> on <code>device_id</code> where <code>is_deleted = false</code> to avoid row multiplication from historical device assignments.</td></tr><tr><td><code>trip_start_time</code></td><td>timestamp</td><td>Time when the trip began. Determined by the first point of a new movement segment after a qualifying stop or data gap.</td></tr><tr><td><code>trip_end_time</code></td><td>timestamp</td><td>Time when the trip ended. Set to the moment the device transitioned from moving to a stop that lasted at least 5 minutes.</td></tr><tr><td><code>trip_duration</code></td><td>text</td><td>Trip duration formatted as <code>HH:MI:SS</code>.</td></tr><tr><td><code>trip_duration_seconds</code></td><td>numeric</td><td>Trip duration in seconds. Use this field for arithmetic calculations.</td></tr><tr><td><code>trip_distance_meters</code></td><td>numeric</td><td>Total trip distance in meters, calculated from the PostGIS line geometry built from all points in the trip.</td></tr><tr><td><code>avg_speed</code></td><td>numeric</td><td>Average speed across all points in the trip, in km/h.</td></tr><tr><td><code>max_speed</code></td><td>numeric</td><td>Maximum speed recorded during the trip, in km/h.</td></tr><tr><td><code>min_speed</code></td><td>numeric</td><td>Minimum speed recorded during the trip, in km/h.</td></tr><tr><td><code>latitude_start</code></td><td>numeric</td><td>Latitude of the first point of the trip, in degrees.</td></tr><tr><td><code>longitude_start</code></td><td>numeric</td><td>Longitude of the first point of the trip, in degrees.</td></tr><tr><td><code>altitude_start</code></td><td>numeric</td><td>Altitude at the start of the trip, in meters above sea level.</td></tr><tr><td><code>latitude_end</code></td><td>numeric</td><td>Latitude of the last point of the trip, in degrees.</td></tr><tr><td><code>longitude_end</code></td><td>numeric</td><td>Longitude of the last point of the trip, in degrees.</td></tr><tr><td><code>altitude_end</code></td><td>numeric</td><td>Altitude at the end of the trip, in meters above sea level.</td></tr><tr><td><code>points_in_trip</code></td><td>integer</td><td>Number of raw telematics points included in the trip. Minimum 2 for a valid trip.</td></tr><tr><td><code>start_zone</code></td><td>text</td><td>Name of the geofence zone that contains the trip start point, if any. Null if the start point does not fall within a defined zone.</td></tr><tr><td><code>end_zone</code></td><td>text</td><td>Name of the geofence zone that contains the trip end point, if any. Null if the end point does not fall within a defined zone.</td></tr></tbody></table>

The examples below show common query patterns. The basic query returns trip records for the last 7 days ordered by device and start time. The second example joins to `raw_business_data.objects` to include the human-readable object label alongside each trip.

{% tabs %}
{% tab title="Basic trip query" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    trip_start_time,
    trip_end_time,
    trip_duration,
    trip_distance_meters,
    avg_speed,
    start_zone,
    end_zone
FROM processed_common_data.trips
WHERE trip_start_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, trip_start_time;
```
{% endcode %}
{% endtab %}

{% tab title="With object label" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    t.device_id,
    o.object_label,
    t.trip_start_time,
    t.trip_end_time,
    t.trip_distance_meters,
    t.avg_speed,
    t.start_zone,
    t.end_zone
FROM processed_common_data.trips t
LEFT JOIN raw_business_data.objects o ON o.device_id = t.device_id
WHERE t.trip_start_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY t.device_id, t.trip_start_time;
```
{% endcode %}
{% endtab %}
{% endtabs %}

## How trips are built

A trip record is not a raw device reading, it is a derived entity assembled from hundreds\
of individual telematics points. The transformation works through the raw data in stages:\
first deciding which points are worth keeping, then classifying each point's movement status,\
then grouping points into trip segments, and finally computing the aggregate metrics that\
appear in the output table. Understanding this process helps you interpret results correctly\
and recognize when the default settings need adjusting for your fleet.

Here are the steps of the algorithm that forms a trip record:

{% stepper %}
{% step %}
#### **Collecting and cleaning the input**

The transformation reads the last 12 hours of data from two source tables:

* `tracking_data_core` for position and motion readings
* `states` for the hardware movement flag each tracker sends

Points are filtered before any further processing:

* Records with zero coordinates are dropped.
* Points with fewer than 3 satellites are excluded from the GNSS set.
* LBS points (`satellites = -1`) are the exception: they pass through for separate handling in the next step, as cell-tower positions require different validity checks before they can participate in trip detection.
{% endstep %}

{% step %}
#### **Deciding whether each point represents movement**

Each point is classified into one of three statuses:

* `moving`: speed is at or above 3 km/h, and either the distance from the previous point exceeds 50 meters or the tracker's hardware movement flag is active. Both conditions prevent false positives: the distance check filters speed spikes, and the hardware flag catches genuine low-speed movement that distance alone would miss.
* `stopped`: speed is below the threshold or distance is insufficient.
* `parked`: no speed reading is available for this point.

LBS points go through an additional check before receiving a status. Any LBS point where the calculated speed between consecutive positions exceeds 200 km/h is discarded as a positioning artifact. An LBS point that passes this check and immediately follows a GNSS point may form its own single-point trip segment, covering cases where a device transitions from satellite to cell-tower positioning mid-journey. All other invalid LBS points are removed.
{% endstep %}

{% step %}
#### Grouping points into trips

With every point carrying a movement status, the transformation identifies trip boundaries. A new trip begins when:

* The device produces its first data point in the processing window.
* Movement resumes after a qualifying stop (minimum parking duration exceeded).
* Movement resumes after a data gap (timeout exceeded).

A trip ends when:

* The device has been continuously stopped for the minimum parking duration.
* A data gap of the timeout length occurs while the device is not moving.

The thresholds that control these boundaries are listed in the [Configuration parameters](trips.md#configuration-parameters) reference below.
{% endstep %}

{% step %}
#### Computing output metrics

Once trip boundaries are established, all points in each trip are aggregated into a single output row:

* `trip_distance_meters`: derived from a PostGIS line geometry built from the ordered point sequence using `ST_MakeLine`.
* `avg_speed`, `max_speed`, `min_speed`: computed across all points in the trip.
* `latitude_start`, `longitude_start`, `latitude_end`, `longitude_end`: taken from the first and last points in the sequence.
* `start_zone`, `end_zone`: populated by matching start and end coordinates against `processed_common_data.zones_geom`. A match requires the point to fall exactly within the zone boundary; no buffer is applied.
{% endstep %}

{% step %}
#### Filtering out noise before writing results

Not every trip segment that the algorithm identifies is worth keeping. Short segments caused by brief GPS recovery, stationary devices with occasional position drift, or incomplete data at the window boundary are discarded. A trip is only written to the output table when it meets all of the following conditions simultaneously:

| Condition              | Threshold                     |
| ---------------------- | ----------------------------- |
| Points in trip         | At least 2                    |
| Maximum speed recorded | At least 3 km/h               |
| Total trip distance    | At least 100 meters           |
| Trip start time        | Must be determined (not null) |
| Trip end time          | Must be determined (not null) |
{% endstep %}
{% endstepper %}

The following two reference sections contain the configurable parameters and the raw data\
scaling rules that the transformation applies internally.

<details>

<summary>Configuration parameters</summary>

These thresholds govern how trips are split and validated. They are the most common targets for customization. See [Customizing the transformation](trips.md#customizing-the-transformation) for instructions on how to adjust them in the workflow template.

<table><thead><tr><th width="233">Parameter</th><th width="133">Default value</th><th>Description</th></tr></thead><tbody><tr><td>Minimum parking duration</td><td>300 seconds (5 minutes)</td><td>A stop shorter than this does not end the current trip. The device is considered to still be on the same journey.</td></tr><tr><td>Data gap timeout</td><td>1200 seconds (20 minutes)</td><td>If no data arrives for this duration, the current trip ends and a new one begins when data resumes.</td></tr><tr><td>Minimum movement distance</td><td>50 meters</td><td>A point must be at least this far from the previous point to count as movement.</td></tr><tr><td>Minimum movement speed</td><td>3 km/h</td><td>Points below this speed are not classified as moving.</td></tr></tbody></table>

</details>

<details>

<summary>Raw value scaling</summary>

Raw telematics data from `tracking_data_core` stores coordinate and speed values as integers for efficient storage. The transformation converts them to standard units before processing.

| Field                   | Divisor          | Result                                 |
| ----------------------- | ---------------- | -------------------------------------- |
| `latitude`, `longitude` | 10,000,000 (1e7) | Degrees (e.g., 554567890 → 55.456789°) |
| `altitude`              | 10,000,000 (1e7) | Meters above sea level                 |
| `speed`                 | 100 (1e2)        | km/h (e.g., 6530 → 65.30 km/h)         |

</details>

## Customizing the transformation

The default Trips transformation reflects Navixy's general-purpose trip detection logic. If your operational scenario requires different behavior, you can load the workflow template into [Transformation Builder](../transformation-builder/), modify the relevant nodes, and schedule the adjusted workflow as a custom transformation in `processed_custom_data`.

{% hint style="warning" %}
Several nodes in the Trips workflow use PostGIS geometry functions and window functions (`LAG`, `FIRST_VALUE`, `LAST_VALUE`, `SUM OVER`). Modifying these nodes requires solid SQL knowledge. Always preview the result using the **Execute** button in Transformation Builder before scheduling a modified workflow.
{% endhint %}

### Customization examples

Expand the sections below to see some case-based customization examples.

<details>

<summary>Adjusting stop and gap thresholds</summary>

Use this when the default 5-minute parking threshold splits what should be one continuous trip into several records — common in urban delivery routes with frequent short stops. This adjustment also helps when devices have irregular reporting intervals and the 20-minute gap timeout ends trips prematurely.

The threshold values are hardcoded as integer constants in two Custom SQL nodes:

1. Open the **Trip Flags** node (`custom_9`). Find the condition `parking_duration >= 300 * INTERVAL '1 second'`. This appears twice in the node. Replace `300` with your target value in seconds in both places. For example, to require a 10-minute stop, use `600`.
2. In the same **Trip Flags** node (`custom_9`), find `time_diff >= 1200`. Then open the **Parking Markers** node (`custom_6`) and find the same condition there. Replace `1200` with your target value in seconds in both nodes. For example, to tolerate 30-minute gaps, use `1800`.
3. Click **Execute** to preview the result and confirm that trip boundaries match your operational expectations before scheduling.

</details>

<details>

<summary>Activating distance-based LBS filtering</summary>

Use this when your fleet operates in areas with poor GPS coverage and LBS points are introducing large coordinate jumps — for example, a device losing satellite signal and snapping to a cell tower several kilometers away. Excluding these points produces cleaner trip geometry and more accurate distance calculations.

The workflow template includes a placeholder for this check in the **LBS Processed** node (`custom_3`), but it is inactive by default. The `distance_status` column is hardcoded to always return `'VALID'` via the stub expression `CASE WHEN 1 != 1 THEN 'EXCLUDED_DISTANCE' ELSE 'VALID' END`. To activate it:

1. Open the **LBS Processed** Custom SQL node (`custom_3`) in Transformation Builder.
2. Replace the stub line with a real condition based on `distance_meters`. For example, to exclude LBS points more than 500 meters from the previous point:

```sql
   CASE
       WHEN point_type = 'LBS' AND distance_meters > 500
       THEN 'EXCLUDED_DISTANCE'
       ELSE 'VALID'
   END AS distance_status,
```

A threshold between 200 and 1000 meters is a reasonable starting range — use a lower value in areas with dense cell tower coverage, higher where towers are sparse.

3. Click **Execute** to preview the result. Check how many LBS points are now excluded and whether the remaining trips look correct. Adjust the threshold if needed before scheduling.

</details>

<details>

<summary>Excluding LBS points entirely</summary>

Use this when your fleet uses GPS-only devices that never produce LBS readings, or when you want trip records built exclusively from confirmed GNSS positions.

1. Open the **Core Data** source node (`telematics_1`, type: Raw data: Telematics). In the **Filter condition** field, remove `OR satellites = -1` from the satellite check, changing:

```
   event_id IN (2, 802, 803, 804, 811) AND latitude <> 0 AND longitude <> 0
   AND (satellites >= 3 OR satellites = -1)
```

to:

```
   event_id IN (2, 802, 803, 804, 811) AND latitude <> 0 AND longitude <> 0
   AND satellites >= 3
```

2. Delete the **LBS Processed** node (`custom_3`) and the **Filtered LBS** node (`custom_4`) from the graph, along with all edges connected to them. Draw a new edge from the **With Distance** node (`custom_2`) to the **Filter Invalid** node (`filter_1`) to restore the data flow.
3. Open the **Filter Invalid** node and replace both existing dynamic conditions with a single direct condition: `calculated_speed_kmh <= 200`. The original conditions (`lbs_excluded_flag = 0` and `speed_status = 'VALID'`) reference columns produced by the removed nodes and will cause an execution error if left in place.
4. Click **Execute** to preview the output. Verify that point counts and trip boundaries look correct for your data before scheduling.

</details>

### Using the workflow template

Navixy provides a ready-made Trips workflow template you can load into Transformation Builder as a starting point. The template covers all processing steps described on this page, from raw data ingestion through zone matching and aggregation.

See the [Templates](../transformation-builder/templates.md) page for the download, import instructions, and the Output node configuration for this transformation.

## Next steps

* [**Common transformations**](./): Back to the transformation index.
* [**Templates**](../transformation-builder/templates.md): Download the Trips workflow template and import it into Transformation Builder.
* [**Transformation Builder**](../transformation-builder/): Learn how to work with the visual workflow editor, add nodes, and preview results.
* [**Raw data layer**](../../bronze-layer.md): Explore the source tables that feed into the Trips transformation: `tracking_data_core`, `states`, and `zones_geom`.
