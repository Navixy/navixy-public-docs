---
description: >-
  Business Data Repository stores the business data of a workspace: assets,
  devices, geo objects, schedules, and your own fields and lists.
tags:
  - overview
  - beta
---

# Business Data Repository overview

**Business Data Repository** is the product that stores the business data of your workspace: what you track, the devices that report on it, the places that matter to it, and its working hours. It's one of the products served by [Navixy GraphQL API](../). A GPS device reports where it is, but it doesn't know that it's fitted to a refrigerated van or which depot the van returns to. Business Data Repository keeps these facts, so the data from your devices is about your vehicles, people, and places. This documentation is for Navixy users who call the API for their own workspace to create and change this data or to read it into their own systems.

All Business Data Repository queries are nested under the `bdr` field of the `Query` type and Business Data Repository mutations under the `bdr` field of the `Mutation` type. Authentication, pagination, and error handling work the same way for every product of Navixy GraphQL API, so see [Authentication](../authentication.md), [Pagination](../pagination.md), and [Error handling](../error-handling.md) to learn that. For the endpoint and request headers, see [Getting started](../getting-started.md).

In Location Intelligence, the same data appears on the **Assets**, **Geo objects**, and **Schedules** screens, and devices appear in Device Management. The API keeps devices and inventories in Business Data Repository too, so one product serves all of them here.

## Purpose and capabilities

Business Data Repository API enables you to:

* Describe the vehicles, people, and equipment you track as assets, sort them by asset type, and gather them into asset groups
* Register GPS devices with their hardware identifiers, keep them in inventories, and assign them to assets
* Mark places, areas, and routes as geo objects, with their shapes in GeoJSON
* Describe working hours, service windows, and other repeating times as schedules
* Add fields of your own to asset types and geo object types
* Keep your own lists and tags, and pick values from them in your fields
* Add existing users to the workspace as members, and register external systems as integrations
* See who created, changed, or deleted a record, and when, in the audit log

Your access token names one workspace, and every operation acts in it. No operation takes a workspace ID, so a request can't read or change the data of another workspace.

## Key concepts

<table><thead><tr><th width="147">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Workspace</strong></td><td>The space that holds the business data of one customer. Navixy Console creates workspaces, so they are read-only in this API.</td></tr><tr><td><strong>Asset</strong></td><td>Something your business tracks, such as a vehicle, a trailer, a person, or a container. It gets its position and readings from the devices assigned to it.</td></tr><tr><td><strong>Asset type</strong></td><td>The template an asset is based on, such as Vehicle or Generator. It decides which fields the asset has. A new workspace starts with three preset asset types: Vehicle, Workforce, and Cargo.</td></tr><tr><td><strong>Asset group</strong></td><td>A set of assets you handle together, such as the vans of one depot. An asset has exactly one type and can be in any number of groups.</td></tr><tr><td><strong>Custom field</strong></td><td>A field of your own on an asset type or a geo object type, such as a license plate or an opening time. Every asset or geo object of that type has it. A field can hold text, a number, yes or no, a date or a date and time, a shape, a value from one of your lists, or a link to a device, a schedule, or another record.</td></tr><tr><td><strong>Device</strong></td><td>A GPS tracker, sensor, or beacon, known by its hardware identifiers, such as an IMEI. You assign a device to an asset through a device field of its asset type. A device can be assigned to one asset at a time, and one asset can have several devices.</td></tr><tr><td><strong>Inventory</strong></td><td>A stock of devices, such as a warehouse. It records which devices it holds, and each device keeps the history of the inventories it was assigned to.</td></tr><tr><td><strong>Geo object</strong></td><td>A place, an area, or a route on the map, such as a depot, a customer site, or a delivery zone. Its shape is stored as GeoJSON. Like an asset, it's based on a type that decides its fields.</td></tr><tr><td><strong>Schedule</strong></td><td>When something happens and how often it repeats, such as a depot's opening hours or a team's shift. Its format is compatible with iCalendar. One schedule can be assigned to any number of assets and geo objects.</td></tr><tr><td><strong>Catalog</strong></td><td>A list of values, such as your asset types, the device models, or your tags. Navixy provides the system catalogs, and you can create your own.</td></tr><tr><td><strong>Member</strong></td><td>A person who works in the workspace.</td></tr><tr><td><strong>Integration</strong></td><td>An external system with its own access to the API, such as your ERP.</td></tr><tr><td><strong>Audit event</strong></td><td>A record of one change: who made it, what changed, and when.</td></tr></tbody></table>

## How the data fits together

Assets are at the center. Devices, geo objects, and schedules are assigned to assets through the fields of their asset type.

1. Navixy Console creates the workspace. When Console activates it, Business Data Repository creates the three preset asset types.
2. You choose or create the asset types you need and add your own fields to them. You create geo object types the same way.
3. You register devices and assign them to assets. From then on, [Tracking](../tracking/README.md) follows each asset through its devices.
4. You create geo objects and schedules and assign them to assets through their fields. A geo object can have a schedule too, such as a depot's opening hours.
5. Business Data Repository records who creates, changes, or deletes a record, and when, in the audit log. Some changes, such as adding an asset to a group, aren't recorded. To learn what is recorded, see [Which entities are audited](guides/investigating-changes-with-audit-logs.md#which-entities-are-audited).

## Who does what

Three parties share the work on the business data of a workspace:

<table><thead><tr><th width="200">Who</th><th>Does</th></tr></thead><tbody><tr><td><strong>You</strong></td><td>Create and change assets and their types and groups, devices and inventories, geo objects, schedules, lists, members, and integrations. Read the audit log. Every call acts in the workspace named by the token.</td></tr><tr><td><strong>Navixy Console</strong></td><td>Creates the workspace and decides which products it runs. Business Data Repository shows the workspace but can't change it.</td></tr><tr><td><strong>Tracking</strong></td><td>Reads the assets and their devices to show live positions, trips, and stops.</td></tr></tbody></table>

Business Data Repository stores schedules but doesn't act on them. What a schedule means, such as whether a vehicle may move outside a shift, is up to the product that reads it.

A record that other records point at can't be deleted. The link can be one of their own fields, such as the type of an asset or the inventory of a device. It can also be a custom field value, such as an asset that names a schedule, or a custom field definition that names the record. In a workspace that has IoT Logic, a device also can't be deleted while a flow lists it, whether the flow is enabled or not. The delete is refused with `IN_USE`, and the error names the records to change first. When a device is deleted, the custom fields that don't require a device lose it; a field that requires one refuses the delete instead.

## Navigation

The Business Data Repository documentation has eight guides and an API reference with one page per topic.

The guides cover the tasks that you can do with the Business Data Repository API for your workspace:

* [Activating a device](guides/activating-a-device.md): Activate a batch of GPS devices, correct a wrong SIM number, resend a lost SMS, and see which devices are still waiting to connect.
* [Managing device records and identifiers](guides/managing-device-records-and-identifiers.md): Register a device and manage its hardware identifiers.
* [Managing device inventory](guides/managing-device-inventory.md): Create inventories, move devices between them, and read the history of each move.
* [Creating assets and assigning devices](guides/creating-assets-and-assigning-devices.md): Create an asset and assign a device to it.
* [Organizing assets into groups](guides/organizing-assets-into-groups.md): Gather assets into groups and change who is in them.
* [Defining and using custom fields](guides/defining-and-using-custom-fields.md): Add a field to a type and fill it in on a record. To find records by the values of their fields, see [Custom field filtering and sorting](../filtering-and-sorting/custom-field-filtering.md).
* [Defining recurring schedules](guides/defining-recurring-schedules.md): Create a schedule that repeats, change it, and delete it.
* [Creating geofences, POIs, and routes](guides/creating-geofences-pois-and-routes.md): Create geo objects of different shapes.
* [Investigating changes with audit logs](guides/investigating-changes-with-audit-logs.md): Find who changed a record, what they changed, and when.

The [Business Data Repository API reference](api-reference/README.md) documents the operations and types, one page per topic:

* [Workspaces](api-reference/workspaces/): Your workspace and its members.
* [Actors](api-reference/actors/): The users and integrations that send requests.
* [Devices](api-reference/devices/): Devices, their identifiers, the device catalogs, and inventories.
* [Assets](api-reference/assets/): Assets, asset types, and asset groups.
* [Geo objects](api-reference/geo-objects.md): Geo objects and their types.
* [Schedules](api-reference/schedules.md): Schedules and their calendar format.
* [Custom fields](api-reference/custom-fields.md): Field definitions, the field types, and the values they hold.
* [Audit](api-reference/audit.md): Audit events and the queries that read them.
* [Catalogs](api-reference/catalogs/): Catalogs and their items, tags, and the system catalogs.

For the types that Business Data Repository shares with the other products and for an index of the whole API, see [Core API reference](../core-api-reference/).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
