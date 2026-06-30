---
description: The Transformation layer reshapes raw IoT Query data into analytics-ready entities. Browse pre-built transformations and understand how data is processed
---

# Transformation layer

{% hint style="info" %}
#### Coming soon!

The Transformation layer architecture described on this page is currently in development. While the core transformation capabilities are operational, implementation details may evolve before final release. If you are interested in early access or have questions, contact [iotquery@navixy.com](mailto:iotquery@navixy.com).
{% endhint %}

## What the Transformation layer does

The **Transformation layer** processes raw data from the **Raw data layer** into normalized, query-ready analytical entities. Where the Raw data layer contains everything captured from devices and systems (individual points, events, and field values useful for verification and troubleshooting), the Transformation layer turns that raw data into meaningful objects such as trips, zone visits, and operational states.

{% hint style="info" icon="lightbulb-exclamation" %}
Transformation layer in brief: the Raw data layer is everything collected, the Transformation layer is what you can work with.
{% endhint %}

This intermediate layer eliminates repetitive manual data preparation and makes your data ready for practical analytics. Fleet operators can answer common operational questions without extensive data processing, and integrators gain a stable foundation for building scalable reporting and BI solutions.

Transformations can be designed and configured using the [**Transformation Builder**](transformation-builder/), a visual tool that allows you to create custom analytical entities through a drag-and-drop workflow interface. For details on how to build and manage transformations, see the Transformation Builder documentation.

Navixy provides a set of ready-made common transformations that run automatically and populate the `processed_common_data` schema without any configuration on your part. You can query this data immediately after connecting to IoT Query. Each common transformation also comes with a workflow template you can load into Transformation Builder to inspect the processing logic or adapt it to your specific scenario. See [Common transformations](common-transformations/) for available entities and their output schemas.

## How data is organized

The Transformation layer uses a dynamic schema approach where database structures form automatically based on active transformations. Unlike the Raw data layer with its fixed schema definitions, the Transformation layer contains only the tables that correspond to transformations that are currently active. The available tables and their structures depend on which transformations are configured in your **IoT Query** instance.

Transformation layer data is organized into two PostgreSQL schemas: `processed_common_data` and `processed_custom_data`.

### processed\_common\_data

The `processed_common_data` schema contains transformations developed and maintained by Navixy. This schema is shared across all clients and provides standardized analytical entities that address common telematics use cases. Tables appear in this schema as Navixy deploys new transformations to serve widely applicable analytical requirements.

{% hint style="warning" %}
The `processed_common_data` schema is **read-only for external clients**. You can query the data freely, but you cannot modify tables, insert records, or alter structures in this schema. To build your own analytical entities, use the `processed_custom_data` schema through the [Transformation Builder](transformation-builder/).
{% endhint %}

### processed\_custom\_data

The `processed_custom_data` schema contains client-specific transformations created to address unique business requirements. Each client has an isolated instance of this schema, so your data is not visible to other organizations. Tables in this schema correspond to transformations you configure and manage through the [Transformation Builder](transformation-builder/).

You have full ownership of this schema: you decide which transformations to create, how they process data, and when they run. The Transformation Builder generates the configuration and SQL needed to produce your custom analytical entities.

#### What happens when transformations change

When you activate a transformation, the system automatically creates the corresponding table structure in the appropriate schema. When transformations are deactivated or removed, their tables may be archived or deleted based on data retention policies.

This dynamic formation is why the Transformation layer does not provide fixed schema descriptions like the Raw data layer does. The available tables and their structures reflect the specific transformations configured for your IoT Query instance.

## Data processing characteristics

Transformation layer entities are maintained automatically through scheduled processes. When you query this data, consider the following processing characteristics.

* **Scheduled updates.** Each transformation processes new Raw data layer records according to its configured schedule. Updates typically occur hourly or every few hours, depending on transformation complexity and configuration.
* **Processing windows.** Transformations operate on time-based segments to efficiently process manageable portions of data rather than scanning entire datasets. This approach balances processing performance with data freshness.
* **Recalculation behavior.** When configuration changes trigger a recalculation, recent data may show brief inconsistencies during active processing windows. These inconsistencies resolve automatically once the processing cycle completes.
* **Schema-specific behavior.** Transformations in `processed_common_data` update simultaneously for all clients sharing that schema, since Navixy manages the schedule centrally. Transformations in `processed_custom_data` execute independently per client, allowing you to customize scheduling and processing logic for your specific needs.

## What to consider when querying

When writing SQL queries against Transformation layer data, keep these points in mind:

* **Use the full schema.table format.** Always reference tables with their schema prefix to avoid ambiguity:

```sql
SELECT *
FROM processed_common_data.table_name
WHERE device_time >= CURRENT_DATE - INTERVAL '7 days';
```

* **Include time-range filters.** Add time-based conditions in your `WHERE` clauses to limit the volume of data scanned. This improves query performance and reduces execution time.
* **Check transformation schedules.** The data in Transformation layer tables reflects the most recent completed processing cycle. If you need data that is only a few minutes old, the Raw data layer may be more appropriate until the next transformation cycle runs.
* **Remember that `processed_common_data` is read-only.** Use this schema for querying standardized entities maintained by Navixy. To create your own analytical entities, configure transformations in the `processed_custom_data` schema through the [Transformation Builder](transformation-builder/).

## Next steps

* [**Transformation Builder**](transformation-builder/): Design custom analytical entities using the visual workflow interface.
* [**Raw data layer**](../bronze-layer.md): Explore the source schemas (`raw_telematics_data` and `raw_business_data`) that feed into transformations.
* [**SQL Recipe Book**](../../../example-queries/): Learn query patterns and best practices for working with Transformation layer tables in Dashboard Studio.
* [**Common transformations**](common-transformations/): Explore the ready-made analytical entities available in `processed_common_data`, including output schemas, query examples, and Transformation Builder templates.
