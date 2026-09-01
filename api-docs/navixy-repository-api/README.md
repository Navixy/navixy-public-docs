# Overview

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

**Navixy Repository API** is a programming interface for managing the business entities of the Navixy platform. It provides a flexible way to define what you track (assets), how data is collected (devices), where important points are located (geo objects), and when things happen (schedules).

**Navixy Repository API is based on GraphQL.** Unlike REST APIs where you call multiple endpoints to gather related data, GraphQL allows you to request the exact fields you need in a single query. You describe the shape of the data you want, and the API returns it in that shape.

For a quick description of GraphQL concepts, see [GraphQL basics](graphql-basics/README.md).

## Purpose and capabilities

**Navixy Repository API** enables you to:

- Create **assets** and organize them into **groups**
- Register **devices** (GPS trackers, sensors) with hardware identifiers and add them to **inventories**
- Define **geo objects** (geofences, points of interest, routes) with GeoJSON geometry
- Create **schedules** for work hours, maintenance windows, and time-based rules
- Configure **custom fields** to store your own workspace-specific data on assets and geo objects

## Key concepts

The API is organized around the following core resources:

<table><thead><tr><th width="147.48895263671875">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Workspace</strong></td><td>The tenant that owns every other resource: members, assets, devices, geo objects, and schedules. Navixy Console provisions workspaces, so they are read-only in this API.</td></tr><tr><td><strong>Asset</strong></td><td>A business object you're tracking: a vehicle, piece of equipment, employee, or any other entity. Assets can be linked into <strong>asset groups</strong> or assigned one or multiple GPS devices.</td></tr><tr><td><strong>Device</strong></td><td>Physical tracking hardware (GPS tracker, sensor, beacon). Devices have types, models, statuses, and hardware identifiers (IMEI, serial number).</td></tr><tr><td><strong>Inventory</strong></td><td>A logical grouping of devices for stock management (warehouse, vehicle stock, field inventory).</td></tr><tr><td><strong>Geo object</strong></td><td>A location-based entity based on the GeoJSON standard: geofence, point of interest, or route.</td></tr><tr><td><strong>Schedule</strong></td><td>iCalendar-compatible time-based schedules for your operations (maintenance cycles, time shifts).</td></tr></tbody></table>

## Navigation

The Navixy Repository API documentation is organized into two sections: concept articles and the API reference.

### Concepts and guides

These articles provide essential background knowledge and guidelines:

- [GraphQL basics](graphql-basics/README.md): A brief introduction to GraphQL for developers familiar with REST APIs.
- [GraphQL tips and patterns](graphql-basics/graphql-tips-and-patterns.md): Practical suggestions for improving your GraphQL experience.
- [Error handling](error-handling.md): Error structure, codes, and common error scenarios.
- [Pagination](pagination.md) and [Filtering and sorting](filtering-and-sorting.md): How to page through large result sets and narrow them down by criteria and order.
- [Optimistic locking](optimistic-locking.md): How the API handles concurrent updates to prevent conflicting changes from overwriting each other.
- [Limits](limits.md): Request, pagination, and input size limits, and the errors they return.
- [Guides](guides/): In-depth guides exploring the most common use cases.

### Core API reference

The [API reference](core-api-reference.md) provides complete technical specifications for all GraphQL types and operations, grouped by category:

- [Common resources](common.md)
- [Directives](directives.md)
- [Workspaces](workspaces/)
- [Actors](actors/)
- [Devices](devices/)
- [Assets](assets/)
- [Geo objects](geo-objects.md)
- [Schedules](schedules.md)
- [Custom fields](custom-fields.md)
- [Audit](audit.md)
- [Catalogs](catalogs/)

{% hint style="warning" %}
The API supports [GraphQL introspection](graphql-basics/README.md#introspection) for authenticated users. You can also check the public [GraphQL schema](https://raw.githubusercontent.com/Navixy/navixy-api/refs/heads/master/docs/navixy-repository-api/graphql-schema/schema.graphql).
{% endhint %}
