---
description: >-
  How the Input change events table detects discrete input changes and how to
  query it.
---

# Input change events

The Input change events transformation records every time a device's discrete input changes state and that change matches a configured `input_change` rule. Each row in `processed_common_data.input_change_events` represents one matched change: which input flipped, its new value, when and where it happened, and which rule it satisfied.

Like Rule-based driver events, Input change events runs on a fixed schedule rather than continuously. A refresh function re-evaluates a window of raw data every 15 minutes, so a new row can lag up to 15 minutes behind the actual input change on the device.

{% hint style="info" %}
* All timestamps are stored in UTC.
* Only changes that match a configured `input_change` rule produce a row. Discrete input activity that doesn't match any rule is still available in `raw_telematics_data.additional_data`, just not surfaced here.
{% endhint %}

## Output table: processed\_common\_data.input\_change\_events

Each row represents one detected input change that matched an `input_change` rule. The table is keyed on `device_id`, `device_time`, and `rule_id`.

<table><thead><tr><th width="180">Field</th><th width="100">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>device_id</code></td><td>integer</td><td>Device identifier.</td></tr><tr><td><code>object_id</code></td><td>integer</td><td>Object (vehicle/asset) identifier associated with the device.</td></tr><tr><td><code>object_label</code></td><td>text</td><td>Human-readable object name. Already denormalized into this table, so unlike Trips, no join to <code>raw_business_data.objects</code> is needed to display it.</td></tr><tr><td><code>device_time</code></td><td>timestamp</td><td>Moment the input change occurred on the device.</td></tr><tr><td><code>rule_id</code></td><td>integer</td><td>Identifier of the <code>input_change</code> rule that matched.</td></tr><tr><td><code>input_number</code></td><td>integer</td><td>Input number, 1-based, matching the position in the <code>discrete_inputs</code> bit string in <code>raw_telematics_data.additional_data</code>.</td></tr><tr><td><code>input_value</code></td><td>integer</td><td>New value of the input after the change: <code>0</code> or <code>1</code>.</td></tr><tr><td><code>event_type</code></td><td>text</td><td>Always <code>input_change</code>.</td></tr><tr><td><code>event_comment1</code></td><td>text</td><td>First comment configured on the matched rule.</td></tr><tr><td><code>event_comment2</code></td><td>text</td><td>Second comment configured on the matched rule.</td></tr><tr><td><code>zone_id</code></td><td>integer</td><td>Identifier of the geofence zone the device was in when the change occurred. Null if the rule isn't zone-linked.</td></tr><tr><td><code>zone_label</code></td><td>text</td><td>Name of that zone. Null if the rule isn't zone-linked.</td></tr><tr><td><code>latitude</code></td><td>numeric</td><td>Latitude at the moment of the change, in degrees.</td></tr><tr><td><code>longitude</code></td><td>numeric</td><td>Longitude at the moment of the change, in degrees.</td></tr><tr><td><code>created_at</code></td><td>timestamp</td><td>When the row was written by the scheduled refresh.</td></tr></tbody></table>

The examples below show common query patterns. The basic query returns recent input changes ordered by device and time. The second filters to a specific input number and value. The third filters to a specific rule.

{% tabs %}
{% tab title="Basic query" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    object_label,
    device_time,
    input_number,
    input_value,
    zone_label
FROM processed_common_data.input_change_events
WHERE device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}
{% endtab %}

{% tab title="Filter by input" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    object_label,
    device_time,
    input_value,
    zone_label
FROM processed_common_data.input_change_events
WHERE input_number = 3
  AND input_value = 1
  AND device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}
{% endtab %}

{% tab title="Filter by rule" %}
{% code overflow="wrap" expandable="true" %}
```sql
SELECT
    device_id,
    object_label,
    device_time,
    input_number,
    input_value
FROM processed_common_data.input_change_events
WHERE rule_id = 12345
  AND device_time >= CURRENT_DATE - INTERVAL '7 days'
ORDER BY device_id, device_time;
```
{% endcode %}
{% endtab %}
{% endtabs %}

## How input changes are detected

A row in this table isn't written the instant an input changes on the device. It is written on the next scheduled refresh, which re-evaluates a window of raw data against each device's last known state.

{% stepper %}
{% step %}
#### Scheduled refresh

Every 15 minutes, the refresh function reads the most recent window of rows from `raw_telematics_data.additional_data`.
{% endstep %}

{% step %}
#### Comparing against the last known state

Each device's current `discrete_inputs` and `discrete_outputs` bit strings are compared, bit by bit, against the state recorded on the previous refresh. Only bits that actually flipped are considered further.
{% endstep %}

{% step %}
#### Matching against rules

A flipped bit becomes a candidate event if its input number and new value match the condition configured on an `input_change` rule. See [Input triggering](../../../../../user-guide/guide/events-and-notifications/inputs-and-outputs/input-triggering.md) for how these rules are configured, and the [Rule types reference](https://navixy.com/docs/navixy-api/user-api/backend-api/resources/tracking/tracker/rules/rule_types) for the full list of rule types.
{% endstep %}

{% step %}
#### Zone check

If the matched rule is linked to a geofence, the refresh confirms the device was inside that zone at the time of the change before writing a row. Rules that aren't zone-linked skip this check.
{% endstep %}

{% step %}
#### Updating the last known state

Once a refresh completes, each device's latest bit state is recorded for comparison on the next run.
{% endstep %}
{% endstepper %}

## Customizing

Input change events isn't customized through a Transformation Builder workflow the way Trips is. The rows that appear here directly reflect whichever `input_change` rules are configured on your account: to change what gets recorded, add, edit, or remove the corresponding rules rather than a transformation template. See [Input triggering](../../../../../user-guide/guide/events-and-notifications/inputs-and-outputs/input-triggering.md) for how to configure these rules.

## Next steps

* [**Common transformations**](./): Back to the transformation index.
* [**Trips**](trips.md): A sibling transformation that also runs on a schedule against raw telematics data.
* [**Rule-based driver events**](rule-based-driver-events.md): A sibling transformation that records driving violations on the same 15-minute schedule.
* [**Raw data layer**](../../bronze-layer.md): Explore `additional_data`, the source table that feeds Input change events.
* [**Input triggering**](../../../../../user-guide/guide/events-and-notifications/inputs-and-outputs/input-triggering.md): Configure the `input_change` rules that determine which input changes are recorded here.
