---
description: >-
  How the Driver performance events table detects driving violations in real
  time and how to query it.
---

# Driver performance events

The Driver performance events transformation detects driving violations as they happen and stores them as individual event records. Each row in `processed_common_data.driver_performance_events` represents one violation: what type it was, when and where it occurred, and how fast the vehicle was moving at the time.

Unlike Trips and Sensor data aggregation, which run on a fixed schedule and reprocess a data window, Driver performance events populates continuously. A trigger fires immediately after each new telematics record, ignition state change, or sensor reading is written, and a matching event appears in the table within microseconds.

{% hint style="info" %}

* Repeated events of the same type from the same device within 3 seconds are combined into a single row, since a device can send multiple signals for one incident.
* Accelerometer-based events are ignored while the vehicle is moving below 3 km/h, and seatbelt events are ignored while the vehicle is stationary. This avoids flagging vibration from loading or parking as a violation.
* All timestamps are stored in UTC.
  {% endhint %}

## Output table: processed\_common\_data.driver\_performance\_events

Each row represents one detected violation, except idle events, where a single row is updated in place for as long as the vehicle keeps idling. The table is keyed on `device_id`, `device_time`, and `event_id`.

<table><thead><tr><th width="180">Field</th><th width="100">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>device_id</code></td><td>integer</td><td>Device identifier. To get the object label, join to <code>raw_business_data.objects</code> on <code>device_id</code> where <code>is_deleted = false</code> to avoid row multiplication from historical device assignments.</td></tr><tr><td><code>device_time</code></td><td>timestamp</td><td>Event timestamp. For most violation types, the moment the event occurred. For idle events, the start of the continuous ignition-on period. The row updates in place as idling continues, but <code>device_time</code> itself does not change.</td></tr><tr><td><code>event_id</code></td><td>integer</td><td>Raw event type identifier. Matches the source event ID for hardware-detected violations, or a synthetic ID (9001, 9002) for violations derived from ignition state or sensor readings.</td></tr><tr><td><code>event_type</code></td><td>text</td><td>Human-readable violation label. See <a href="#violation-types">Violation types</a> below for the complete list of values.</td></tr><tr><td><code>speed_kmh</code></td><td>numeric</td><td>Vehicle speed at the moment of the event, in km/h. Null for idle and RPM exceeded events, which are not speed-based.</td></tr><tr><td><code>latitude</code></td><td>numeric</td><td>Latitude in degrees. For idle and RPM exceeded events, taken from the nearest raw telematics point within 5 minutes of the event. Null if no point falls within that window.</td></tr><tr><td><code>longitude</code></td><td>numeric</td><td>Longitude in degrees. Uses the same nearest-point lookup as latitude for idle and RPM exceeded events.</td></tr><tr><td><code>extra_value</code></td><td>numeric</td><td>Context-dependent value. For idle events, the idle duration in seconds so far, increasing as the idle period continues. For RPM exceeded events, the RPM reading that triggered the event. Null for all other violation types.</td></tr><tr><td><code>created_at</code></td><td>timestamp</td><td>When the row was written. For idle events, this reflects the first detection of the idle period, not the most recent update.</td></tr></tbody></table>

### Violation types

The table below lists every violation type the transformation detects, the exact `event_type` value stored for it, and the condition that triggers it. Use the `event_type` column value directly when filtering or grouping queries.

<table><thead><tr><th width="180">Category</th><th width="260">event_type value</th><th width="90">event_id</th><th width="110">Source</th><th>Condition</th></tr></thead><tbody><tr><td>Harsh acceleration</td><td><code>Driver performance acceleration</code></td><td>990</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Harsh braking</td><td><code>Driver performance braking</code></td><td>991</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Harsh turn</td><td><code>Driver performance turn</code></td><td>989</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Acceleration in turn</td><td><code>Driver performance acceleration and turn</code></td><td>987</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Braking in turn</td><td><code>Driver performance braking and turn</code></td><td>988</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Fast lane change</td><td><code>Driver performance quick lane change</code></td><td>900</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Harsh driving (general)</td><td><code>Driver performance</code></td><td>996</td><td>Accelerometer</td><td>Speed at or above 3 km/h</td></tr><tr><td>Unbuckled seatbelt</td><td><code>seatbelt</code></td><td>931</td><td>Hardware</td><td>Vehicle is moving (speed above 0)</td></tr><tr><td>Speeding</td><td><code>overspeeding</code></td><td>950</td><td>Hardware</td><td>Speed above 70 km/h</td></tr><tr><td>Idle over 25 minutes</td><td><code>idle over 25 min</code></td><td>9001 (synthetic)</td><td>Ignition state</td><td>Ignition on continuously for 25 minutes or more</td></tr><tr><td>RPM exceeded</td><td><code>rpm exceeded</code></td><td>9002 (synthetic)</td><td>RPM sensor</td><td>Reading above 5,000 RPM</td></tr></tbody></table>

The examples below show common query patterns. The basic query returns recent violations ordered by device and time. The second joins to `raw_business_data.objects` to include the object label. The third filters to the harsh driving categories only.

{% tabs %}
{% tab title="Basic query" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    device_time,
    event_type,
    speed_kmh,
    latitude,
    longitude
FROM processed_common_data.driver_performance_events
WHERE device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}
{% endtab %}

{% tab title="With object label" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    d.device_id,
    o.object_label,
    d.device_time,
    d.event_type,
    d.speed_kmh
FROM processed_common_data.driver_performance_events d
LEFT JOIN raw_business_data.objects o
    ON o.device_id = d.device_id
   AND o.is_deleted = false
WHERE d.device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY d.device_id, d.device_time;
```
{% endcode %}
{% endtab %}

{% tab title="Harsh driving only" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    device_time,
    event_type,
    speed_kmh
FROM processed_common_data.driver_performance_events
WHERE event_type IN (
    'Driver performance acceleration',
    'Driver performance braking',
    'Driver performance turn',
    'Driver performance acceleration and turn',
    'Driver performance braking and turn',
    'Driver performance quick lane change',
    'Driver performance'
)
AND device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}
{% endtab %}
{% endtabs %}

## How violations are detected

A row in this table is not written on a schedule. It is written the moment a qualifying condition is met, by one of three triggers watching different source tables. Understanding which trigger produced a given row helps you interpret its fields correctly.

{% stepper %}
{% step %}
#### Hardware and accelerometer events

A trigger on `tracking_data_core` fires after each insert and checks whether the incoming event matches one of the recognized violation codes. Accelerometer-based events are skipped when the vehicle is moving below 3 km/h. Seatbelt events are skipped when the vehicle is stationary. Overspeeding events are skipped unless speed exceeds 70 km/h.
{% endstep %}

{% step %}
#### Deduplication

Before inserting a row, the trigger checks whether the same device already logged the same `event_type` within the past 3 seconds. If so, the new occurrence is skipped. This prevents a single incident from producing multiple rows when a device sends repeated signals for it.
{% endstep %}

{% step %}
#### Idle detection

A separate trigger on `states` fires when ignition turns on. It finds the start of the current continuous ignition-on period and records an event only once that period reaches 25 minutes. Unlike other violation types, this event is not deduplicated. Instead, the same row is updated so `extra_value` keeps growing for as long as the vehicle keeps idling.
{% endstep %}

{% step %}
#### RPM detection

A separate trigger on `inputs` fires on each new sensor reading. It checks that the reading comes from an RPM sensor and exceeds 5,000 RPM, then applies the same 3-second deduplication window used for hardware events.
{% endstep %}

{% step %}
#### Location lookup

Idle and RPM events do not originate from a positioning record, so the trigger looks up the nearest `tracking_data_core` point within 5 minutes of the event to fill in latitude and longitude. If no point falls within that window, location is left null.
{% endstep %}
{% endstepper %}

<details>

<summary>Configuration parameters</summary>

These thresholds govern which raw records become violation events. See [Customizing the transformation](#customizing-the-transformation) for how to adjust them in a workflow template.

<table><thead><tr><th width="220">Parameter</th><th width="160">Default value</th><th>Description</th></tr></thead><tbody><tr><td>Minimum speed for accelerometer events</td><td>3 km/h</td><td>Accelerometer-based violations below this speed are ignored, to avoid flagging vibration while parked or loading.</td></tr><tr><td>Speeding threshold</td><td>70 km/h</td><td>Vehicle speed must exceed this value for an overspeeding event to be recorded.</td></tr><tr><td>Idle duration threshold</td><td>1,500 seconds (25 minutes)</td><td>Minimum length of a continuous ignition-on period before an idle event is recorded.</td></tr><tr><td>RPM threshold</td><td>5,000 RPM</td><td>Engine RPM must exceed this value for an RPM exceeded event to be recorded.</td></tr><tr><td>Deduplication window</td><td>3 seconds</td><td>Repeated events of the same type from the same device within this window are merged into one.</td></tr></tbody></table>

</details>

## Customizing the transformation

The default Driver performance events transformation reflects Navixy's standard thresholds. If your operational scenario requires different values, for example a lower speed floor for fleets that operate at low speeds, or a shorter idle threshold, contact **iotquery@navixy.com** to request the corresponding workflow template.

{% hint style="warning" %}
The built-in triggers that populate `processed_common_data.driver_performance_events` cannot be edited directly. Customizing thresholds means building a parallel workflow in Transformation Builder and scheduling it as a separate transformation in `processed_custom_data`. It does not change the table described on this page.
{% endhint %}

Once you have the template, load it into [Transformation Builder](../transformation-builder/), adjust the relevant thresholds, and schedule the modified workflow as a custom transformation.

## Next steps

* [**Common transformations**](./): Back to the transformation index.
* [**Trips**](trips.md): A sibling transformation that produces vehicle trip records from raw telematics data.
* [**Sensor data aggregation**](sensor-data-aggregation.md): A sibling transformation that aggregates sensor readings into time buckets.
* [**Raw data layer**](../../bronze-layer.md): Explore `tracking_data_core`, `states`, and `inputs`, the source tables that feed Driver performance events.
* [**Transformation Builder**](../transformation-builder/): Learn how to build a customized version of this transformation.
* [**Driving Score Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/12-driver-performance-dashboard.md): Uses Driver performance events for fleet-wide driving safety scoring.
* [**Behavior Impact Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/13-behavior-impact-dashboard.md): Uses Driver performance events to track behavior trends over time.
* [**Safety & Security Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/10-premium-safety-security-dashboard.md): Uses Driver performance events alongside geofence and trip data.
* [**Leasing Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/06-leasing-dashboard.md): Uses harsh driving and idle detection queries, currently based on raw telematics rather than Driver performance events.
