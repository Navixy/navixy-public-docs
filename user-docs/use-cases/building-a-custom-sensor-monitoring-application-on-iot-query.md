---
description: >-
  See which Navixy tools let a system integrator build a custom application
  on top of IoT Query. Sensoriqua, a zone-based sensor monitoring app, shows
  the pattern in practice.
---

# Building a sensor monitoring application on IoT Query

This article shows how a system integrator can turn telematics and sensor data already flowing into Navixy into a purpose-built monitoring application, without standing up a separate data pipeline. The reference case is [Sensoriqua](https://marketplace.navixy.com/shop/sensoriqua-sensor-monitoring-app/), a zone-based sensor monitoring application built by a Navixy partner directly on [IoT Query](https://navixy.com/en/iot-query). The architecture, the SQL patterns, and the trade-offs below apply to any custom application built the same way: on top of IoT Query's read-only data layer, with an integrator's own logic layered over it.

## What is Sensoriqua?

Sensoriqua is a reference implementation: a zone-based sensor monitoring application built by a Navixy partner directly on the IoT Query PTL, not a Navixy product itself. It groups individual sensor channels by physical zone (a cold room, a server rack row, a greenhouse sector), applies a threshold to each one, and shows the zone's operational status ahead of the underlying raw values.

[Read more](https://navixy.com/en/blog/sensor-monitoring-system-application) about the story behind this development in the Navixy blog.

### Why do warehouses need a zone-based view instead of a per-device dashboard?

An operations team monitoring cold rooms or storage zones usually needs one answer: which zones are within their configured range right now. A dashboard organized by GPS device and sensor ID answers a different question, and forces the operator to translate device IDs into physical locations every time. The same problem shows up beyond warehouses:

* Pharmaceutical and food warehouse clients need temperature and humidity records organized by storage zone for GxP or cold-chain compliance audits, where the relevant question is which zone stayed within range, not which device reported which value.
* Industrial clients group equipment-condition data by production line, workshop, or building wing instead of by individual asset.

IoT Query supplies the underlying telematics and sensor data in both cases. The integrator decides how the application maps those readings to the client's zones and operating concepts.

### Why build directly on the IoT Query database instead of through an API?

A REST API exposes a fixed set of endpoints and response shapes. A requirement that falls outside that set usually means waiting for a new endpoint, or working around the existing ones. IoT Query exposes the same data as a PostgreSQL database instead, so the same requirements become one more SQL query instead of an API change:

* A new time aggregation, such as 1-minute averages instead of the default.
* A different sparkline window.
* An unplanned historical range a client didn't ask for at launch.

That's the trade-off IoT Query is built around: a consistent, read-only column structure across `raw_business_data` and `raw_telematics_data` that stays stable to build against, while the integrator decides what to compute from it. Sensoriqua's zone thresholds, its sparkline windows, and its daily aggregates are all queries against that same structure, not a purpose-built API Navixy would need to extend for the next client's requirement.

## What does Navixy provide for a custom sensor monitoring application?

Sensoriqua is built from seven pieces. Two, App Connect and direct PostgreSQL access to IoT Query, are Navixy platform capabilities used as they are. Historical data queries and geographic filtering follow directly from that same database access. The remaining three, the zone and sensor configuration schema, client-side threshold evaluation, and report exports, are patterns the integrator builds on top.

* **App Connect authentication**: the application authenticates through a user's existing Navixy session instead of a separate login.
* **Direct PostgreSQL queries to IoT Query**: sensor and telematics readings are queryable through SQL, not through a fixed set of API endpoints.
* **Historical data queries**: raw readings stay queryable over the retention window a client's IoT Query subscription plan provides.
* **Geographic filtering of monitored objects**: each object's latest GPS position lives in the same database as its sensor readings, so a map view is a join away rather than a separate integration.
* **Daily-summary report exports:** built by the integrator. Sensoriqua aggregates and renders its own reports in the browser.
* **A persistent zone and sensor configuration schema**: built by the integrator, not provided by Navixy. It maps devices and channels to a client's physical zones.
* **Client-side threshold evaluation**: also built by the integrator, comparing scaled readings against the configured thresholds in the browser.

## How does App Connect authenticate a custom application without a separate login?

[Navixy App Connect](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications/navixy-app-connect) is authentication middleware that lets a third-party application use a user's existing Navixy session instead of a separate login. During sign-in, the application's backend sends the user's Navixy session key to App Connect, which converts it into a JWT that carries the user's platform permissions. Sensoriqua combines this authenticated session with a direct query against IoT Query, so an operator configuring the sensor monitoring system sees the actual devices present in the client's account, not a static predefined list.

Registering an application this way happens through [User applications](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications/creating-new-user-applications), where the integrator sets the application URL and use a session key as an authentication method. It passes the current user's session, so the application respects that user's role and object permissions. Use this when different operators at a client should see different subsets of zones or objects.

A multi-tenant monitoring application serving several warehouse clients, the way Sensoriqua does, needs the session-key method so that one client's operators can't see another client's zones through a shared API key.

## How does the raw data layer expose sensor readings for direct SQL access?

IoT Query's [raw data layer](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/bronze-layer) exposes sensor readings in `raw_telematics_data.inputs` (`device_id`, `device_time`, `sensor_name`, `value`) and the metadata that gives those readings meaning in `raw_business_data.sensor_description` (`sensor_label`, `input_label`, `multiplier`, `divider`, `parameters`). The `value` column is stored as `text`, so cast it explicitly before scaling or comparing it to a threshold. Join both to `raw_business_data.objects` to resolve a device to the object label an operator recognizes:

```sql
SELECT
    o.object_label,
    sd.sensor_label,
    i.value::numeric * sd.multiplier / NULLIF(sd.divider, 0) AS scaled_value,
    i.device_time
FROM raw_telematics_data.inputs i
JOIN raw_business_data.objects o
    ON o.device_id = i.device_id
JOIN raw_business_data.sensor_description sd
    ON sd.device_id = i.device_id AND sd.input_label = i.sensor_name
WHERE i.device_id = 10234
AND i.device_time > now() - interval '1 hour'
AND i.value ~ '^-?[0-9]+(\.[0-9]+)?$'
ORDER BY i.device_time DESC
LIMIT 20;
```

From the application's perspective, these tables stay read-only. The monitoring application adds a visualization and configuration layer on top, rather than creating another copy of the telematics data.

## How does a persistent sensor map decouple zones from GPS devices?

A zone-based application needs its own configuration store for the mapping that IoT Query doesn't have an opinion about: which sensor belongs to which zone, and what range counts as normal. Sensoriqua keeps this map in the application's own database, separate from IoT Query, since IoT Query's raw tables stay read-only from the application's side. Each configured sensor stores:

* The object and source column it reads from (`device_id` and `sensor_name`, matching `raw_telematics_data.inputs`).
* A human-readable display label.
* A multiplier used as a scaling factor before display, for unit conversion or calibration.
* MIN and MAX thresholds that define the operating range used for visual status.
* A sparkline depth (1, 2, 4, or 8 hours) that controls how much history the sparkline widget displays.

The following schema illustrates the shape of that mapping. It's an architecture pattern, not a Navixy specification:

```sql
-- Illustrative: lives in the application's own database, not in IoT Query
CREATE TABLE sensor_map (
    id serial PRIMARY KEY,
    plane_id integer NOT NULL,          -- dashboard plane, e.g. a physical zone
    device_id integer NOT NULL,         -- matches raw_telematics_data.inputs.device_id
    sensor_name text NOT NULL,          -- matches inputs.sensor_name / sensor_description.input_label
    display_label text NOT NULL,
    multiplier numeric NOT NULL DEFAULT 1,
    min_threshold numeric,
    max_threshold numeric,
    sparkline_hours integer NOT NULL DEFAULT 1
);
```

Sensors are organized into named dashboard planes, panels that correspond to physical zones such as a cold room or a server rack row. A single dashboard can contain multiple planes, and each plane holds the sensors assigned to that location. This separates how operators work with the monitoring interface from the GPS device structure used to transport the original readings: renaming a zone or reassigning a sensor in the application never touches IoT Query.

Don't confuse this application-level threshold with `sensor_description.parameters`, a platform-level JSONB field that also carries `min` and `max` keys. Those bound the raw value Navixy accepts as valid for a sensor, primarily for fuel-level validation, not the operating range a client defines for a zone. The two live at different layers and serve different purposes.

## How is live zone status calculated without server-side state?

The live board polls recent values at a configurable interval, 30 seconds, 1 minute, or 5 minutes, and evaluates thresholds in the browser after each poll. Query only a recent time window so each poll stays cheap regardless of how much history `raw_telematics_data.inputs` holds, and use `DISTINCT ON` to keep just the latest reading per device and sensor:

```sql
SELECT DISTINCT ON (i.device_id, i.sensor_name)
    i.device_id,
    i.sensor_name,
    i.value::numeric AS raw_value,
    i.device_time
FROM raw_telematics_data.inputs i
WHERE i.device_id = ANY(:device_ids)
  AND i.device_time > now() - interval '5 minutes'
  AND i.value ~ '^-?[0-9]+(\.[0-9]+)?$'
ORDER BY i.device_id, i.sensor_name, i.device_time DESC;
```

The backend joins this result against the application's own `sensor_map` in application code, not in the same SQL query, since the two tables live in separate PostgreSQL instances. The frontend then applies each sensor's multiplier and compares the scaled value against its MIN and MAX bounds. A value outside either bound turns that sensor red. If any sensor in a zone is red, the zone panel is red. If every configured sensor is within range, the panel is green. A sensor with no configured threshold shows neutral.

This design keeps the backend stateless with respect to zone health: it stores configuration and retrieves readings, while the current zone status is recalculated from the latest successful poll. The trade-off is that the application doesn't record the moment a threshold was crossed, doesn't push notifications, and doesn't queue alerts. If a poll fails, the displayed values stay unchanged until the next successful request. See [When does zone logic belong in the transformation layer instead of the browser?](#when-does-zone-logic-belong-in-the-transformation-layer-instead-of-the-browser) below if a client needs any of those.

## How do history queries and daily reports turn raw readings into trends?

A history view answers a different question than the live board: not "is this zone in range now," but "how did this sensor behave over the last week." Query `raw_telematics_data.inputs` for a specific object, sensor, and time range, up to the retention window your IoT Query subscription plan provides:

```sql
SELECT
    date_trunc('day', device_time) AS day,
    min(value::numeric) AS min_value,
    max(value::numeric) AS max_value,
    avg(value::numeric) AS avg_value
FROM raw_telematics_data.inputs
WHERE device_id = 10234
  AND sensor_name = 'temperature_1'
  AND device_time BETWEEN now() - interval '30 days' AND now()
  AND value ~ '^-?[0-9]+(\.[0-9]+)?$'
GROUP BY 1
ORDER BY 1;
```

Sensoriqua renders this as an interactive chart with threshold bands overlaid, and exports daily aggregates as JSON, HTML and PDF with embedded charts, or XLSX, generated in the browser rather than through a separate server-side rendering step.

Before building this export logic from scratch, check whether [Dashboard Studio's Reports](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio/creating-reports) already cover the requirement. A report built from a single SQL query already produces a coordinated data table, chart, and location map, and exports to HTML, Excel, and PDF. For a client whose reporting needs stop at daily aggregates and don't require the zone-based live board, Dashboard Studio may remove the need to build a custom reporting layer at all.

## How does the map view add geographic context to threshold exceptions?

`raw_telematics_data.tracking_data_core` carries each object's latest GPS position, stored as latitude and longitude scaled by 10,000,000 for precision. Join it to `raw_business_data.objects` and divide back down to decimal degrees to plot monitored objects on a map:

```sql
SELECT DISTINCT ON (t.device_id)
    o.object_id,
    o.object_label,
    t.latitude::float / 10000000 AS latitude,
    t.longitude::float / 10000000 AS longitude,
    t.device_time
FROM raw_telematics_data.tracking_data_core t
JOIN raw_business_data.objects o
    ON o.device_id = t.device_id
WHERE t.device_id = ANY(:device_ids)
  AND device_time BETWEEN now() - interval '30 days' AND now()
ORDER BY t.device_id, t.device_time DESC;
```

Sensoriqua's map plots each object's latest position together with its current threshold status from the live board, and lets an operator filter by business entity and threshold condition. For a client running several warehouses or technical sites, this turns "which zone is out of range" into "where is that site." The object doesn't need to move for this to matter: a stationary gateway can still identify a fixed warehouse or technical site on the map.

## What should an integrator check before relying on direct SQL access?

IoT Query's [getting started](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/getting-started) and [connection setup](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/connection-setup) pages document constraints that shape how a polling application like this should be built, beyond the SQL itself:

* **Each client gets a dedicated database instance.** A monitoring application serving multiple clients needs one connection configuration per client, not a single shared pool that assumes multi-tenant rows in one database. The application's own `sensor_map` needs the same per-client scoping.
* **Historical data availability depends on the client's platform subscription plan.** Confirm the actual retention window before a history view or a compliance report promises a fixed range like 90 days.
* **SQL error messaging is minimal.** Build client-side validation and clear error states in the application rather than relying on IoT Query to explain a malformed query.
* **Manage idle connections and use connection pooling.** A live board polling every 30 seconds across many devices can accumulate idle connections quickly without pooling.
* **Never hardcode IoT Query credentials.** They are separate from the App Connect session that authenticates the application's users, and need their own secret storage.

## When does zone logic belong in the transformation layer instead of the browser?

Client-side threshold evaluation keeps Sensoriqua's backend simple: no scheduler, no history of threshold crossings, no alerting to maintain. That trade-off stops working once a client needs an audit trail of when a zone went out of range, or a notification the moment it happens.

Two existing Navixy tools cover that need without turning the monitoring application itself into an automation engine:

* [Transformation Builder](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/transformation-layer/transformation-builder) (under development, with early access on request) can compute zone status as a scheduled SQL workflow instead of a browser-side calculation, writing a timestamped record every time a status changes. [This use case](customizing-trip-detection-for-your-business.md) walks through building and scheduling a workflow this way, including how to version its logic as YAML.
* [IoT Logic](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/iot-logic) evaluates rules against the same incoming data stream independently of any visualization application, and can send notifications or trigger device actions when a condition is met.

Both keep threshold automation separate from the application that displays the data, the same separation Sensoriqua's client-side design already assumes. Add either one when a client's requirement moves from "show me current status" to "tell me the moment it changes."

## How can an AI agent use this article to scaffold a similar application?

Every schema name, join, and API reference in this article points at a real Navixy resource, precise enough for an AI assistant, generator, or agent to draft a working prototype directly from it: a backend that queries IoT Query with the patterns shown above, an application-side configuration table for zones and thresholds, and a frontend that polls and evaluates them. Two things still need to come from a person before that draft is worth running against a client's data.

First, the concrete connection details: an agent can't guess a client's IoT Query host, database name, or credentials, or which authentication method (session key or API key) its App Connect registration actually uses. Supply these explicitly rather than letting an agent invent placeholder values it then treats as real.

Second, the operational facts that decide the shape of the application:

* What the client considers a zone: a room, a rack row, a whole site.
* What counts as a threshold breach for each sensor type, and whether that threshold is regulatory (GxP, cold-chain) or operational.
* Whether the client needs alerting the moment a threshold is crossed, which points to IoT Logic instead of more browser-side logic.
* How far back historical readings need to go, bounded by the client's actual subscription plan.
* Which export formats the client's reporting workflow already expects (JSON, HTML, PDF, XLSX), and whether Dashboard Studio's Reports already cover it.
* Whether the application serves one client or several, which decides whether per-client connection and configuration scoping is a requirement from day one.

An agent that has these answers can generate a prototype whose queries and configuration schema match this article's patterns. An agent that doesn't will still produce code that runs, against a data model and a threshold logic it invented rather than the client's actual operation.

## What are common problems when building on IoT Query directly, and how are they fixed?

* **The live board shows stale values with no error.** Polling failed silently and the display kept the last successful response. Add an explicit staleness indicator based on the age of the last successful poll, since IoT Query itself won't push a failure notice.
* **A zone flips between green and red every few seconds.** The reading is oscillating near a threshold boundary. Add hysteresis in the client-side comparison, for example requiring two consecutive readings past the bound before changing status.
* **The same sensor type shows different units across two warehouses.** The `multiplier` in `sensor_map` (or in `sensor_description`) is configured differently, or a sensor's calibration wasn't verified against its user manual. Check both configurations before assuming a data problem.
* **A history chart cuts off earlier than expected.** The requested range exceeds what the client's platform subscription plan retains, not a query error.
* **One client's operator can see another client's zones.** The application authenticates through App Connect with an API key instead of a session key, or the `sensor_map` table isn't scoped per client.
* **Nobody finds out a zone went out of range until someone opens the dashboard.** This is an inherent limit of stateless, client-side threshold evaluation, not a bug. Add IoT Logic or a scheduled transformation-layer workflow for that requirement instead of polling faster.

## What does this pattern give an integrator?

Everything a custom application like Sensoriqua needs already exists on the Navixy platform: session-based authentication through App Connect, direct SQL access to a per-client, isolated IoT Query database, historical retention bounded by a documented subscription plan, and Dashboard Studio's ready-made reporting for an integrator who'd rather not build exports from scratch.

What is specific to sensor monitoring, and to Sensoriqua as one implementation of it, is the layer built on top of that foundation:

* **Zone definitions**: which sensors and devices belong to which physical zone.
* **Breach criteria**: the threshold or condition that counts as a breach for a given sensor and client.
* **Retention needs**: how long history has to stay queryable for that client.
* **Notification requirements**: whether a breach gets logged, triggers a notification, or both.

Navixy provides the authenticated, isolated, query-ready data layer. Building the domain-specific logic on top of it, for a warehouse, a production line, or any other zone-based workflow, is the integrator's own, reusable work.

## See also

* [IoT Query](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/): Overview of IoT Query, its layered data model, and who it serves.
* [Getting started with IoT Query](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/getting-started): Connection requirements and current platform limitations.
* [Connection setup](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/connection-setup): PostgreSQL connection parameters, security, and troubleshooting.
* [Raw data layer](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/iot-query/schema-overview/bronze-layer): Full schema reference for `raw_business_data` and `raw_telematics_data`.
* [Navixy App Connect](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications/navixy-app-connect#what-is-navixy-app-connect): Authentication middleware for third-party applications.
* [Creating new User applications](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications/creating-new-user-applications): Registering an application URL and choosing an authentication method.
* [Dashboard Studio: Creating reports](https://app.gitbook.com/s/oFNFEIINiGFbhi3Px3dE/dashboard-studio/creating-reports): Built-in reporting with table, chart, and map export, an alternative to a custom reporting layer.
* [IoT Logic](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/iot-logic): Rule-based automation and notifications on the same data stream.
* [Customizing trip detection in telematics software](customizing-trip-detection-for-your-business.md): Use case about building and scheduling custom logic in the transformation layer, and versioning it as YAML.

