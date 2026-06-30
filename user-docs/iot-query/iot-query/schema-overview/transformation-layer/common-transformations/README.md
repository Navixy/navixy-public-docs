---
description: Ready-made analytical entities in the Transformation layer, available to query immediately and adaptable through Transformation Builder
---

# Common transformations

The Transformation layer includes a set of common transformations developed and maintained by Navixy. These transformations run automatically on a schedule and populate the `processed_common_data` schema with normalized analytical entities derived from raw telematics and business data.

You can start querying this data immediately after connecting to IoT Query — no configuration required. Each transformation produces a specific table with a documented output schema, update schedule, and processing logic.

{% hint style="info" %}
To build a custom version of any listed transformation, download the corresponding workflow template from the [Templates](../transformation-builder/templates.md) page and import it into Transformation Builder.
{% endhint %}

## Available transformations

The table below lists transformations Navixy maintains in `processed_common_data`. Each entry links to a dedicated page covering the output schema, the algorithm that produces the rows, and the customization options you can apply when building a derived workflow in `processed_custom_data`.

<table><thead><tr><th width="148">Transformation</th><th width="273">Table</th><th>Description</th></tr></thead><tbody><tr><td><a href="trips.md">Trips</a></td><td><code>processed_common_data.trips</code></td><td>Vehicle trips derived from raw telematics data, including start and end times, distance, speed statistics, and zone detection.</td></tr><tr><td><a href="sensor-data-aggregation.md">Sensor data aggregation</a></td><td><code>processed_common_data.sensors_data_by_hours</code></td><td>Hourly aggregates of sensor readings, including average, minimum, and maximum values, fuel calibration interpolation, and human-readable enrichment.</td></tr></tbody></table>

## Next steps

* [**Trips**](trips.md): Output schema, algorithm description, customization options, and workflow template download.
* [**Sensor data aggregation**](sensor-data-aggregation.md): Output schema, algorithm description, bucket size variations, and workflow template download.
* [**Templates**](../transformation-builder/templates.md): Download workflow templates for common transformations and import them into Transformation Builder.
* [**Transformation layer**](../): How the Transformation layer organizes processed data into schemas and how to query it.
* [**Transformation Builder**](../transformation-builder/): Design and adapt transformation workflows using the visual interface.
