---
description: >-
  See how a trip is modeled point by point from speed, ignition, and sensor
  thresholds, and how that same threshold model can be versioned as YAML
  instead of hardcoded.
---

# Modeling trips through thresholds and moving status

A tracker doesn't report a trip. It reports a point: a coordinate, a speed, a timestamp, and whatever sensors are wired to it. A trip is a model built on top of that stream, one threshold decision at a time: a speed threshold decides whether a point counts as movement, a duration threshold decides how long a stop has to last before that movement ends, and a sensor threshold decides whether the same point also counts as a violation. This article walks through that threshold model layer by layer, then shows [Trips Intelli](https://marketplace.navixy.com/shop/trips-intelli/), a free, open-source application built on [IoT Query](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/), taking the same model far enough to answer a cold-chain investigator's question: what exactly happened at 14:22. [Customizing trip detection in telematics software](customizing-trip-detection-for-your-business.md) covers how to version the built-in Trips workflow as YAML; this article applies the same versioning approach to every threshold the model adds along the way.

## Why is a trip a modeled entity, not a raw signal?

Nothing in a tracker's data stream is labeled "trip." The [Trips transformation](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/trips) builds that label from raw points in stages: first deciding whether each point represents movement, then grouping consecutive points into trip boundaries, then aggregating the result into distance, speed, and duration.

The first stage is a threshold decision applied to a single point. A point is `moving` when speed is at or above 3 km/h and either the distance from the previous point exceeds 50 meters or the tracker's hardware movement flag is active. It's `stopped` when speed or distance falls short of that. The second stage is a threshold decision applied to a run of points: a stop shorter than the minimum parking duration (5 minutes by default) doesn't end the trip, and a data gap shorter than the timeout (20 minutes by default) doesn't start a new one. Change either number, and the same raw stream of points produces a different set of trips. That's the entire model: points classified by threshold, then grouped by threshold.

## How do thresholds and settings turn a point into a moving-status label?

The platform's own [Parking Detection](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/parking-detection-logic) settings are the same model, exposed as configuration rather than SQL. Four settings decide when a unit counts as parked:

* **Minimum inactivity detection**: how long the unit must stay idle before the platform marks it parked (1 to 1440 minutes).
* **Maximum idle speed**: the speed below which the platform treats a reading as idle.
* **Consider ignition status**: whether engine state factors into the decision, when a reliable ignition sensor is configured.
* **Consider motion sensor**: whether the tracker's own reported movement state factors in, to catch cases GPS noise would otherwise misclassify.

Without ignition or motion data, the logic is exactly the point-and-run threshold model from the previous section: speed drops below the idle threshold, the platform starts counting, and if the inactivity threshold is met before a packet arrives above the speed threshold, the unit is marked parked. Adding ignition doesn't replace that logic, it adds a second threshold on top of it: the same "stopped for 5 minutes" condition means something different with the engine off than with it running.

## How does ignition add an engine-on parking state?

Speed and duration alone can't distinguish a stop where the engine is off from one where it's idling with refrigeration running. Trips Intelli's trip segmentation labels every stop as `MOVING`, `PARKING`, or `ENGINE_ON_PARKING`, which is the ignition threshold from Parking Detection made explicit in the trip record instead of left as a platform setting. A point can be classified this way with a standalone query:

```sql
-- Extends the Trips workflow's point-classification step
SELECT
   g.device_id,
   g.device_time,
   CASE
       WHEN g.speed * 0.01 >= 3
            AND (
                ST_DistanceSphere(
                    ST_MakePoint(g.longitude/1e7, g.latitude/1e7),
                    ST_MakePoint(g.prev_lon,      g.prev_lat)
                ) > 50
                OR mv.value IN ('1','true')
            )
           THEN 'MOVING'
       WHEN ign.value IN ('1','true')
           THEN 'ENGINE_ON_PARKING'
       ELSE 'PARKING'
   END AS trip_status
FROM (
   SELECT *,
       LAG(latitude/1e7)  OVER (PARTITION BY device_id ORDER BY device_time) AS prev_lat,
       LAG(longitude/1e7) OVER (PARTITION BY device_id ORDER BY device_time) AS prev_lon
   FROM raw_telematics_data.tracking_data_core
   WHERE device_id = ANY(:device_ids)
     AND device_time >= :from_ts AND device_time < :to_ts
     AND event_id IN (2, 802, 803, 804, 811)
     AND (latitude <> 0 OR longitude <> 0)
) g
LEFT JOIN LATERAL (
   SELECT value FROM raw_telematics_data.states
   WHERE device_id = g.device_id AND state_name = 'moving'
     AND device_time <= g.device_time
   ORDER BY device_time DESC LIMIT 1
) mv ON true
LEFT JOIN LATERAL (
   SELECT value FROM raw_telematics_data.states
   WHERE device_id = g.device_id AND state_name = 'ignition_on'
     AND device_time <= g.device_time
   ORDER BY device_time DESC LIMIT 1
) ign ON true
ORDER BY g.device_id, g.device_time;
```

For each point, the query takes the latest `moving` and `ignition_on` records from `raw_telematics_data.states` at or before that point's `device_time`. The threshold is only as reliable as the ignition state behind it: an unconfigured or intermittent ignition input produces a confident-looking `ENGINE_ON_PARKING` label that's wrong, not an error you'd notice. Validate the ignition signal before trusting the third state, the same caution [Parking detection logic](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/parking-detection-logic#how-the-logic-changes-when-considering-ignition) gives for enabling ignition at the platform level.

## How does a sensor threshold attach meaning to the same point?

Movement status is one threshold decision made about a point. A sensor reading is another, made independently. [`processed_common_data.rule_based_driver_events`](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/rule-based-driver-events) already applies this model to speed and RPM: a value crosses a configured limit, and the event carries the geofence the tracker was in at that moment. The same model extends to any channel by querying `raw_telematics_data.inputs` directly instead of waiting for a rule:

```sql
WITH temperature_readings AS (
   SELECT
       i.device_id,
       i.device_time,
       i.value::float AS temperature
   FROM raw_telematics_data.inputs i
   WHERE i.sensor_name = 'temperature_1'
     AND i.device_time >= now() - interval '7 days'
     AND (i.value::float < 2 OR i.value::float > 8)  -- configured cold-chain range
)
SELECT
   t.device_id,
   o.object_label,
   t.device_time,
   t.temperature
FROM temperature_readings t
JOIN raw_business_data.objects o ON o.device_id = t.device_id
ORDER BY t.device_id, t.device_time;
```

A point that fails this threshold doesn't stop being a point in the trip. It's the same point the movement model already classified, now carrying a second label. Correlating the two, was the vehicle moving, parked, or engine-on-parking when the sensor crossed its threshold, is a join on `device_id` and `device_time` against whatever produced the trip segment, not a separate investigation.

## How does a discrete threshold model a door state?

A threshold doesn't need a range to be a threshold. A door sensor reports 0 or 1, and the meaningful moment is the flip, not the level. [`processed_common_data.input_change_events`](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/input-change-events) already models that flip: a bit changes, the change matches a configured `input_change` rule, and the event carries the geofence the tracker was in when it happened.

Correlating a door flip with a sensor threshold from the previous section needs the door's state at, or just before, the sensor reading, not a separate list of door events:

```sql
SELECT
   t.device_id,
   t.device_time,
   t.temperature,
   ic.input_value AS door_state,   -- 1 = open, 0 = closed
   ic.zone_label
FROM temperature_readings t
LEFT JOIN LATERAL (
   SELECT input_value, zone_label
   FROM processed_common_data.input_change_events e
   WHERE e.device_id = t.device_id
     AND e.input_number = 1              -- the input mapped to the cargo door
     AND e.device_time <= t.device_time
   ORDER BY e.device_time DESC
   LIMIT 1
) ic ON true
ORDER BY t.device_id, t.device_time;
```

That `LATERAL` join is the same operation as the movement-status join in the previous section, carried forward to a discrete threshold instead of a continuous one: find the most recent state of another threshold-modeled signal, as of this point.

## How is this threshold model versioned instead of hardcoded?

Every threshold covered so far splits into two configuration surfaces, and they version differently.

The movement thresholds, the ignition-aware third state, and the sensor range query are all Custom SQL nodes inside a Transformation Builder workflow. [Customizing trip detection in telematics software](customizing-trip-detection-for-your-business.md#how-can-trip-logic-be-versioned-and-edited-as-yaml) covers this mechanism in full: every workflow exports as YAML, a flat array of nodes and edges that imports back unchanged, diffs cleanly in git, and deploys identically across client accounts. The `3 km/h` movement threshold, the `ENGINE_ON_PARKING` branch, and the cold-chain range are exactly the values that mechanism is built to version: change one number in the exported YAML, re-import, click **Execute**, and the previous value is still visible in the commit history.

A sensor threshold built as a Navixy alert rule instead of a SQL literal versions differently. A rule lives in `raw_business_data.rules`, scoped to objects and zones through `rules2objects` and `rules2zones`, with no YAML export. Changing it means editing the rule through the platform or the API, and tracking that change means recording the previous value outside git, the same way [Parking detection logic](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/parking-detection-logic#best-practices) recommends documenting the previous value, the new value, and the reason before adjusting a device's parking settings. Choose the SQL-literal route when a threshold needs to live in a version-controlled diff. Choose the rule route when it needs to reuse the platform's own alerting and notification channels instead.

## What does this model look like assembled into one trip-level record?

Every threshold above classifies one signal: movement, a sensor range, or a door bit. Trips Intelli's case is what happens when all three get correlated onto the same trip instead of read as three separate charts.

A pharmaceutical shipment's temperature log can pass, 18 hours averaging 3.2°C, well within range, while a 22-minute stop at a loading dock, door open, produced a spike no averaged report caught. WHO guidance on temperature-sensitive pharmaceutical transport (TRS 961, Annex 9) and the EU Good Distribution Practice guidelines (2013/C 343/01) both treat this kind of excursion as an event to investigate, not an average to report. An investigator's actual question, where was the vehicle when the sensor crossed threshold, was the door open, was this a scheduled stop, is answerable only because every signal involved was already threshold-modeled at the point level: movement status from the Trips model, the sensor range from the previous section, and the door state from `input_change_events`. Trips Intelli's own contribution on top of that is a further, unmodeled signal: a per-trip anomaly score from an IsolationForest model trained on the fleet's own trip history, which is not a threshold at all, but a plain-text explanation ("out-of-shift," "unusual duration for this corridor") that helps an analyst prioritize which correlated trips to look at first.

Trips Intelli assembles the record and the risk classification. It does not certify compliance, guarantee a shipment's acceptability, or replace the disposition decision a qualified person makes under the applicable regulation. Modeling every point correctly is what makes that decision possible to make quickly. It isn't the decision itself.

## What needs checking before trusting a point?

* **Filter non-numeric values before casting them.** `raw_telematics_data.inputs.value` is `text`. Casting a non-numeric reading, such as a discrete state instead of a measurement, raises an error and fails the whole query. Filter to numeric readings first, the same way [Sensor data aggregation](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/sensor-data-aggregation#reading-and-filtering-raw-inputs) applies with a regular expression before aggregating.
* **Don't reuse fuel calibration fields for a non-fuel sensor.** `sensor_description.calibration_data` and the `calibrated_volume_*` columns exist for fuel-level interpolation. A temperature or humidity sensor without calibration data returns its raw aggregate in those columns unchanged, which is correct, but only if you know why the fields are there.
* **A null `zone_id` is ambiguous.** For rule-based events, a null `zone_id` means either that the rule has no zones attached, or that it's configured to fire outside its zones. The same ambiguity applies to any zone-scoped threshold you model this way. Join to the rule's zone list when you need to tell the two cases apart, rather than filtering on `zone_id IS NULL` alone.
* **Every point is UTC, but the correlation tolerance is your decision, not the platform's.** Position, sensor, and event timestamps are all stored in UTC, so joining them needs no timezone conversion. It does need an explicit tolerance for "at the same moment," since a `LATERAL` join finds the most recent prior state, not one guaranteed to be within seconds of the point it's being correlated against.
* **A modeled point is evidence, not a verdict.** Classifying a point correctly, moving or parked, in range or not, is what makes an investigation possible. It isn't the same as the compliance decision that follows from it.

## How does an AI agent use this model with Documentation MCP?

An agent working through the [Navixy Documentation MCP](https://app.gitbook.com/s/gh5cGQ23uFYTcp7Fj7Yd/using-navixy-documentation-with-ai) can retrieve this article and every page it links to on demand: the raw data layer's schema, the Trips and Rule-based driver events transformations, and the SQL Recipe Book's existing threshold recipes. That endpoint is public and needs no authentication:

```
https://navixy.com/docs/~gitbook/mcp
```

Retrieving documentation and running the queries in it are two different steps, through two different channels. The Documentation MCP answers "what table has door events and what does `zone_id` mean when it's null." It doesn't run SQL. Executing the queries in this article still needs a direct IoT Query PostgreSQL connection, the same credentials described in [Connection setup](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/connection-setup). A third tool, [Navixy MCP Server](https://app.gitbook.com/s/gh5cGQ23uFYTcp7Fj7Yd/#navixy-mcp), covers a different job again: it gives an agent live, read-only access to a specific account's tracker states, sensor readings, and IoT Logic flows through pre-built operations, not arbitrary SQL. Pick the tool that matches the task instead of assuming one covers all three.

Before generating a threshold-modeled workflow from this article, an agent still needs the operational facts a person supplies, not invents:

* What speed and distance count as movement for this fleet, the same operational facts [Give an agent the operational definition of a trip first](customizing-trip-detection-for-your-business.md#give-an-agent-the-operational-definition-of-a-trip-first) lists for the Trips workflow itself.
* Which sensor channel maps to which cargo compartment, and what range counts as a violation for that specific cargo, not a generic default.
* Whether `ENGINE_ON_PARKING` should itself count toward a violation window, for example refrigeration running on engine power during a stop, or whether it's purely informational.
* What timestamp tolerance counts as "the door was open at that moment" for a correlation join. Too tight a tolerance misses real correlations; too loose a one manufactures false ones.

## What are common threshold-modeling problems, and how are they fixed?

* **Short stops fragment one trip into several.** The minimum parking duration is shorter than the operation's typical stop. This is the same failure mode [Customizing trip detection in telematics software](customizing-trip-detection-for-your-business.md#how-courier-delivery-patterns-change-the-parking-and-gap-thresholds) documents for courier routes: raise the threshold and re-validate against real data.
* **`ENGINE_ON_PARKING` appears on stops where the vehicle was clearly off.** The ignition sensor isn't configured or reports intermittently. Validate it under **Sensors and Buttons** before relying on the third state, the same check parking detection logic recommends before enabling ignition consideration at all.
* **A sensor threshold query fails with an invalid input syntax error.** A non-numeric reading in `inputs.value` reached the cast. Filter the readings to numeric values, or check the sensor's raw values, before assuming the threshold logic is wrong.
* **The door always shows as closed at the moment of a sensor breach.** The correlation tolerance is too tight, or the door's input number doesn't match this device's wiring. Confirm `input_number` against the device's actual configuration before trusting the join.
* **Two queries disagree on whether an event was inside a zone.** One of them filters on `zone_id IS NULL` without checking whether the rule is zone-inverted. See [What needs checking](#what-needs-checking-before-trusting-a-point) above.

## What does this pattern give an integrator?

A trip, a violation, and a door event are the same kind of object: a point, or a run of points, classified by a threshold. Navixy's own transformations already model each one in isolation. `processed_common_data.trips` models movement. `rule_based_driver_events` models a rule-scoped sensor breach. `input_change_events` models a discrete flip. Extending that model with a new threshold, an ignition-aware third state, or a wider sensor range, is the same Custom SQL node change in every case, and it versions the same way, as YAML that diffs in git.

Trips Intelli is what that model looks like taken all the way to a correlated, trip-level record. It doesn't need a different data pipeline to do that. It needs the same points, classified by more thresholds, joined onto one timeline instead of read as separate charts.

## See also

* [Trips](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/trips): The point-classification and trip-boundary algorithm this article's threshold model is built from.
* [Parking detection logic](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/gps-devices/parking-detection-logic): The platform setting behind the moving and parked point classification.
* [Rule-based driver events](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/rule-based-driver-events): The zone-scoped, rule-driven threshold pattern generalized to arbitrary sensor channels.
* [Input change events](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/input-change-events): Discrete door-state changes with geofence context.
* [Sensor data aggregation](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/common-transformations/sensor-data-aggregation): Hourly sensor baselines and the value-decoding rules referenced above.
* [SQL Recipe Book: Logistics](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/example-queries/logistics): The Temperature Violation Events recipe the sensor threshold query builds on.
* [Customizing trip detection in telematics software](customizing-trip-detection-for-your-business.md): How to version Transformation Builder workflows as YAML, and how to hand one to an AI agent.
* [Building a sensor monitoring application on IoT Query](building-a-custom-sensor-monitoring-application-on-iot-query.md): Sensor thresholds, direct SQL access, and App Connect authentication for a different application on the same data layer.
* [Using Navixy documentation with AI](https://app.gitbook.com/s/gh5cGQ23uFYTcp7Fj7Yd/using-navixy-documentation-with-ai): Connecting the Navixy Documentation MCP server to an AI client.
