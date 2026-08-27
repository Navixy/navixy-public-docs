---
description: >-
  How the Rule-based driver events table turns your alert rules into driving
  violation records, and how to query it.
---

# Rule-based driver events

The Rule-based driver events transformation records driving violations as individual event records, evaluated against the alert rules configured on your account. Each row in `processed_common_data.rule_based_driver_events` represents one violation: which rule detected it, what type it was, when and where it occurred, and how fast the vehicle was moving at the time.

Unlike the trigger-based transformation it replaces, this one runs on a fixed schedule. A refresh function re-evaluates a window of recent data every 15 minutes, so a new row can lag up to 15 minutes behind the event on the device.

{% hint style="info" %}

* Thresholds for speeding and idling come from your own alert rules, not from platform-wide defaults. Two devices can produce different events from identical driving if different rules apply to them.
* Repeated hardware events of the same type from the same device within 10 seconds are combined into a single row, since a device can send multiple signals for one incident.
* Accelerometer-based events are ignored while the vehicle is moving below 3 km/h. This avoids flagging vibration from loading or parking as a violation.
* All timestamps are stored in UTC.
  {% endhint %}

## Output table: processed\_common\_data.rule\_based\_driver\_events

Each row represents one detected violation. The table is keyed on `device_id`, `device_time`, `rule_id`, and `hw_event_id`.

Several columns are denormalized copies of values held elsewhere, so the most common dashboard queries need no joins at all: `object_label` comes from the object record, and `speed_limit`, `event_comment1`, and `event_comment2` come from the rule that produced the event.

<table><thead><tr><th width="180">Field</th><th width="100">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>device_id</code></td><td>integer</td><td>Device identifier.</td></tr><tr><td><code>object_id</code></td><td>integer</td><td>Object (vehicle or asset) identifier associated with the device at the time of the event.</td></tr><tr><td><code>object_label</code></td><td>text</td><td>Human-readable object name. Already denormalized into this table, so no join to <code>raw_business_data.objects</code> is needed.</td></tr><tr><td><code>device_time</code></td><td>timestamp</td><td>Event timestamp, in UTC.</td></tr><tr><td><code>rule_id</code></td><td>integer</td><td>The alert rule that produced the event. References <code>raw_business_data.rules</code>. Set to <code>0</code> for <code>rpm_exceeded</code> events, which are not rule-driven. See <a href="#how-rules-shape-the-output">How rules shape the output</a>.</td></tr><tr><td><code>event_type</code></td><td>text</td><td>Violation category. One of four values. See <a href="#violation-types">Violation types</a> below.</td></tr><tr><td><code>hw_event_id</code></td><td>integer</td><td>Hardware event code for accelerometer and sensor-detected violations. <code>0</code> for <code>speedup</code> and <code>idling_soft</code>, which are derived rather than reported by the device.</td></tr><tr><td><code>event_comment1</code></td><td>text</td><td>Free-text label copied from the rule. Set when the rule was configured, and useful for grouping events by your own categories. Null for <code>rpm_exceeded</code>.</td></tr><tr><td><code>event_comment2</code></td><td>text</td><td>Second free-text label copied from the rule. Null for <code>rpm_exceeded</code>.</td></tr><tr><td><code>speed_limit</code></td><td>integer</td><td>The threshold configured on the rule, denormalized from <code>raw_business_data.rules</code>. For <code>speedup</code> rules this is the speed limit in km/h; for <code>idling_soft</code> rules it is the idle tolerance in minutes.</td></tr><tr><td><code>speed_kmh</code></td><td>numeric</td><td>Vehicle speed at the moment of the event, in km/h.</td></tr><tr><td><code>duration_sec</code></td><td>integer</td><td>Length of the violation in seconds, for event types that span a period of time rather than occurring instantaneously.</td></tr><tr><td><code>zone_id</code></td><td>integer</td><td>Geofence in which the event occurred, when the rule is scoped to specific zones. Null when the rule has no zones attached, and also when the rule is configured to fire outside its zones. See <a href="#zone-scoping">Zone scoping</a>.</td></tr><tr><td><code>zone_label</code></td><td>text</td><td>Human-readable geofence name, denormalized alongside <code>zone_id</code>.</td></tr><tr><td><code>latitude</code></td><td>double precision</td><td>Latitude in degrees.</td></tr><tr><td><code>longitude</code></td><td>double precision</td><td>Longitude in degrees.</td></tr><tr><td><code>extra_value</code></td><td>numeric</td><td>Context-dependent measurement, set for event types that record a reading other than speed. For <code>rpm_exceeded</code> events, the RPM reading that triggered the event.</td></tr><tr><td><code>created_at</code></td><td>timestamp</td><td>When the row was written by the refresh function. Later than <code>device_time</code> by up to the length of one refresh cycle.</td></tr></tbody></table>

### Violation types

The table below lists every violation type the transformation records, the exact `event_type` value stored for it, and where its threshold comes from. Use the `event_type` column value directly when filtering or grouping queries.

<table><thead><tr><th width="150">event_type value</th><th width="230">What it means</th><th width="150">hw_event_id</th><th>Threshold</th></tr></thead><tbody><tr><td><code>speedup</code></td><td>Vehicle exceeded the speed limit set on the rule</td><td><code>0</code></td><td>Per rule, in km/h</td></tr><tr><td><code>harsh_driving</code></td><td>Accelerometer or hardware event reported by the device: harsh acceleration, braking, turns, lane changes, and seatbelt violations</td><td><code>900</code>, <code>931</code>, <code>987</code>–<code>991</code>, <code>996</code></td><td>Detected by the device. Ignored below 3 km/h</td></tr><tr><td><code>idling_soft</code></td><td>Vehicle stationary with ignition on for longer than the rule allows</td><td><code>0</code></td><td>Per rule, in minutes</td></tr><tr><td><code>rpm_exceeded</code></td><td>Engine RPM above the platform threshold</td><td><code>9002</code></td><td>Fixed at 5,000 RPM</td></tr></tbody></table>

{% hint style="warning" %}
The `event_type` values, and the `hw_event_id` numbering, differ from the Driver performance events table that this transformation replaces. Queries written against the old table need their filter values updated, not just the table name. In particular, seatbelt violations no longer have their own event type: they arrive as `harsh_driving` with `hw_event_id = 931`.
{% endhint %}

The examples below show common query patterns. The basic query returns recent violations ordered by device and time. The second adds the rule definition behind each event. The third filters to hardware-detected harsh driving only.

{% tabs %}
{% tab title="Basic query" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    object_label,
    device_time,
    event_type,
    speed_kmh,
    speed_limit,
    latitude,
    longitude
FROM processed_common_data.rule_based_driver_events
WHERE device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}
{% endtab %}

{% tab title="With rule details" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    e.device_id,
    e.object_label,
    e.device_time,
    e.event_type,
    e.speed_kmh,
    e.speed_limit,
    e.event_comment1,
    r.maximum AS rule_threshold
FROM processed_common_data.rule_based_driver_events e
LEFT JOIN raw_business_data.rules r
    ON r.rule_id = e.rule_id
WHERE e.device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY e.device_id, e.device_time;
```
{% endcode %}

Use a `LEFT JOIN` rather than an inner join. `rpm_exceeded` events carry `rule_id = 0` and have no matching row in `raw_business_data.rules`, so an inner join silently drops them.
{% endtab %}

{% tab title="Harsh driving only" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    object_label,
    device_time,
    hw_event_id,
    speed_kmh
FROM processed_common_data.rule_based_driver_events
WHERE event_type = 'harsh_driving'
  AND device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}

Filtering on a single `event_type` value replaces the long `IN` list the previous table required, since all accelerometer and hardware violations now share one category. Split them apart with `hw_event_id` when you need the individual manoeuvre.
{% endtab %}
{% endtabs %}

## How rules shape the output

Rows in this table are not produced by platform-wide detection logic. With the exception of `rpm_exceeded`, every event is the result of an alert rule configured on your account, and the rule determines both the threshold and the scope.

{% stepper %}
{% step %}
#### The rule sets the threshold

Each rule stores its threshold in `raw_business_data.rules.maximum`: a speed in km/h for `speedup` rules, a duration in minutes for `idling_soft` rules. The transformation copies that value into `speed_limit` on every row it writes, so you can see what the vehicle was measured against without joining back to the rule.
{% endstep %}

{% step %}
#### The rule sets the scope

A rule applies to the objects linked to it through `rules2objects`, and optionally to the geofences linked through `rules2zones`. A device outside a rule's object list produces no events for that rule, however it is driven.
{% endstep %}

{% step %}
#### The rule supplies its own labels

`event_comment1` and `event_comment2` are free-text fields filled in when the rule is configured. They are copied onto each event so dashboards can display your own terminology without a join.
{% endstep %}

{% step %}
#### The refresh function evaluates the window

Every 15 minutes, `refresh_rule_based_driver_events` re-examines a 40-minute window of recent data and writes any qualifying events. The window is deliberately longer than the interval, so records that reach the platform late are still picked up on a later pass.
{% endstep %}

{% step %}
#### RPM is the exception

`rpm_exceeded` events are not rule-driven. They use a fixed 5,000 RPM platform threshold, carry `rule_id = 0`, and have no `event_comment1` or `event_comment2` values.
{% endstep %}
{% endstepper %}

### Zone scoping

When a rule has geofences attached through `rules2zones`, those zones decide both whether an event is recorded and whether `zone_id` is populated. Zone geometry is read from `processed_common_data.zones_geom`, materialized from `raw_business_data.zones`.

<table><thead><tr><th width="260">Rule configuration</th><th width="200">Events recorded</th><th>zone_id value</th></tr></thead><tbody><tr><td>No zones attached</td><td>Everywhere</td><td>Null</td></tr><tr><td>Zones attached, <code>zone_inverted = false</code></td><td>Only inside the zones</td><td>The zone the event occurred in</td></tr><tr><td>Zones attached, <code>zone_inverted = true</code></td><td>Only outside the zones</td><td>Null</td></tr></tbody></table>

{% hint style="warning" %}
A null `zone_id` is ambiguous on its own: it means either that the rule had no zones, or that the rule fires outside its zones. Filtering with `WHERE zone_id IS NULL` will mix both cases together. Join to `rules2zones` when you need to tell them apart.
{% endhint %}

<details>

<summary>Fixed platform constants</summary>

These values are the same for every account and are not configurable through a rule. Rule-driven thresholds are covered in [How rules shape the output](#how-rules-shape-the-output).

<table><thead><tr><th width="220">Constant</th><th width="160">Value</th><th>Description</th></tr></thead><tbody><tr><td>Minimum speed for accelerometer events</td><td>3 km/h</td><td>Accelerometer-based violations below this speed are ignored, to avoid flagging vibration while parked or loading.</td></tr><tr><td>RPM threshold</td><td>5,000 RPM</td><td>Engine RPM must exceed this value for an <code>rpm_exceeded</code> event to be recorded.</td></tr><tr><td>Hardware deduplication window</td><td>10 seconds</td><td>Repeated hardware events of the same type from the same device within this window are merged into one.</td></tr><tr><td>Refresh interval</td><td>15 minutes</td><td>How often the refresh function runs.</td></tr><tr><td>Refresh lookback</td><td>40 minutes</td><td>How far back each run re-examines data, so late-arriving records are not missed.</td></tr></tbody></table>

</details>

## Customizing the transformation

For `speedup` and `idling_soft`, customization does not require a workflow at all. Both thresholds are read from the alert rule, so changing the rule changes what the transformation records from the next refresh onward. The same applies to scope: adjust the objects or geofences attached to the rule to change which vehicles and areas produce events.

{% hint style="warning" %}
The refresh function that populates `processed_common_data.rule_based_driver_events` cannot be edited directly. Thresholds that are not rule-driven, such as the 3 km/h accelerometer floor or the 5,000 RPM limit, can only be changed by building a parallel workflow in Transformation Builder and scheduling it as a separate transformation in `processed_custom_data`. That does not change the table described on this page.
{% endhint %}

If your operational scenario needs different fixed thresholds, contact **iotquery@navixy.com** to request the corresponding workflow template. Once you have it, load it into [Transformation Builder](../transformation-builder/), adjust the relevant values, and schedule the modified workflow as a custom transformation.

## Next steps

* [**Common transformations**](./): Back to the transformation index.
* [**Trips**](trips.md): A sibling transformation that produces vehicle trip records from raw telematics data.
* [**Sensor data aggregation**](sensor-data-aggregation.md): A sibling transformation that aggregates sensor readings into time buckets.
* [**Raw data layer**](../../bronze-layer.md): Explore `tracking_data_core`, `states`, and `inputs`, the source tables that feed Rule-based driver events.
* [**Transformation Builder**](../transformation-builder/): Learn how to build a customized version of this transformation.
* [**Driving Score Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/12-driver-performance-dashboard.md): Uses Rule-based driver events for fleet-wide driving safety scoring.
* [**Behavior Impact Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/13-behavior-impact-dashboard.md): Uses Rule-based driver events to track behavior trends over time.
* [**Safety & Security Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/10-premium-safety-security-dashboard.md): Uses Rule-based driver events alongside geofence and trip data.
* [**Leasing Dashboard**](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/schemas/06-leasing-dashboard.md): Uses harsh driving and idle detection queries from Rule-based driver events.
