---
description: >-
  Business Data Repository (BDR) manages assets, devices, geo objects,
  schedules, custom fields, and catalogs through Navixy GraphQL API.
---

# Business Data Repository overview

{% include "../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

**Business Data Repository (BDR)** is the product that manages the business entities of the Navixy platform. It is one of the products served by [Navixy GraphQL API](../). It defines what you track (assets), how data is collected (devices), where important points are located (geo objects), and when things happen (schedules).

All BDR operations are nested under the `bdr` field of the `Query` and `Mutation` types. The shared pages of this space cover the request format, [authentication](../authentication.md), [pagination](../pagination.md), and [error handling](../error-handling.md). For your first request, see [Getting started](../getting-started.md).

## Purpose and capabilities

Business Data Repository enables you to:

* Create **assets** and organize them into **groups**
* Register **devices** (GPS trackers, sensors) with hardware identifiers and add them to **inventories**
* Define **geo objects** (geofences, points of interest, routes) with GeoJSON geometry
* Create **schedules** for work hours, maintenance windows, and time-based rules
* Configure **custom fields** to store your own workspace-specific data on assets and geo objects

## Key concepts

The product is organized around the following core resources:

<table><thead><tr><th width="147.48895263671875">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Workspace</strong></td><td>The tenant that owns every other resource: members, assets, devices, geo objects, and schedules. Navixy Console provisions workspaces, so they are read-only in this API.</td></tr><tr><td><strong>Asset</strong></td><td>A business object you're tracking: a vehicle, piece of equipment, employee, or any other entity. Assets can be linked into <strong>asset groups</strong> or assigned one or multiple GPS devices.</td></tr><tr><td><strong>Device</strong></td><td>Physical tracking hardware (GPS tracker, sensor, beacon). Devices have types, models, statuses, and hardware identifiers (IMEI, serial number).</td></tr><tr><td><strong>Inventory</strong></td><td>A logical grouping of devices for stock management (warehouse, vehicle stock, field inventory).</td></tr><tr><td><strong>Geo object</strong></td><td>A location-based entity based on the GeoJSON standard: geofence, point of interest, or route.</td></tr><tr><td><strong>Schedule</strong></td><td>iCalendar-compatible time-based schedules for your operations (maintenance cycles, time shifts).</td></tr></tbody></table>

## Navigation

The Business Data Repository documentation has two sections: guides and the API reference.

### Guides

Step-by-step walkthroughs of the most common scenarios:

* [Working with devices](../bdr/guides/working-with-devices.md) and [Managing device inventory](../bdr/guides/managing-device-inventory.md)
* [Working with assets](../bdr/guides/working-with-assets.md) and [Organizing assets into groups](../bdr/guides/organizing-assets-into-groups.md)
* [Implementing custom fields](../bdr/guides/implementing-custom-fields.md), with [Custom field filtering and sorting](../filtering-and-sorting/custom-field-filtering.md) for queries by custom field values
* [Managing schedules](../bdr/guides/managing-schedules.md)
* [Working with geo objects](../bdr/guides/working-with-geo-objects.md)
* [Tracking changes with audit](../bdr/guides/tracking-changes-with-audit.md)

### Core API reference

The [API reference](api-reference/) provides complete technical specifications for all GraphQL types and operations of the product, grouped by category:

* [Common resources](../core-api-reference/common.md)
* [Directives](../core-api-reference/directives.md)
* [Workspaces](api-reference/workspaces/)
* [Actors](api-reference/actors/)
* [Devices](api-reference/devices/)
* [Assets](api-reference/assets/)
* [Geo objects](api-reference/geo-objects.md)
* [Schedules](api-reference/schedules.md)
* [Custom fields](api-reference/custom-fields.md)
* [Audit](api-reference/audit.md)
* [Catalogs](api-reference/catalogs/)

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-repository-api/graphql-schema/schema.graphql).
{% endhint %}
