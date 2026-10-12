---
description: >-
  Tracking provides the live state of assets and their devices, the stream that
  follows that state, and the trips and stops calculated from device messages.
tags:
  - overview
  - beta
---

# Tracking overview

**Tracking** is the product that answers where your assets are and what their devices report. It is one of the products served by [Navixy GraphQL API](../). [Business Data Repository](../business-data-repository/README.md) stores the records of an asset and of the devices linked to it. Tracking returns what those devices report: position, connectivity, motion, and sensor readings, plus the trips and stops calculated from them.

All Tracking queries are nested under the `tracking` field of the `Query` type. The state stream is a field of the `Subscription` type. The shared pages of this space cover the request format, [authentication](../authentication.md), [pagination](../pagination.md), and [error handling](../error-handling.md). For the endpoint and the headers of a request, see [Getting started](../getting-started.md).

Tracking is a product of your workspace, so every Tracking query and the state stream need Tracking enabled for the workspace in Navixy Console. Otherwise, the request fails with `PRODUCT_DISABLED`. Enable the product in Console and request a new token. See [What your roles and products allow](../authentication.md#what-your-roles-and-products-allow).

## Purpose and capabilities

Tracking enables you to:

- Read the current state of one asset, of every asset in a workspace, or of a single device
- Follow that state as it changes, without polling for it
- Read the trips and stops of an asset or a device over a period of time
- Read the thresholds that decide whether a device is connected, moving, stopped, or parked

## Key concepts

<table><thead><tr><th width="147.48895263671875">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Device state</strong></td><td>The last known values for one device: where it is, whether it is reporting, whether it is moving, and what its sensors last sent.</td></tr><tr><td><strong>Asset state</strong></td><td>The states of the devices linked to one asset, as separate entries. Tracking doesn't merge them, because two devices on one asset can report the same value differently.</td></tr><tr><td><strong>Reading</strong></td><td>A named value that a device sent, such as an ignition state or a fuel level. Every reading includes the moment it arrived, so the readings of one state can differ in age.</td></tr><tr><td><strong>Field dictionary</strong></td><td>The catalog of reading names. It gives a name a title, a unit, and a kind, so a client can render a reading it has never seen. The dictionary has no entries, so a reading has no title, unit, or kind from it.</td></tr><tr><td><strong>Stream</strong></td><td>A subscription that pushes the whole state of a followed asset, with its linked devices, every time a device reports. A client replaces its copy of the state and keeps no update logic of its own.</td></tr><tr><td><strong>Trip</strong></td><td>A segment of movement between two stops. Trips aren't stored: Tracking calculates them from the messages of a device when you ask for them.</td></tr><tr><td><strong>Stop</strong></td><td>A period during which a device stayed in one place. Calculated on request, in the same way as a trip.</td></tr></tbody></table>

## Device states

A device has three states, and each one changes on its own:

- The **lifecycle** state records whether a device is registered and active. It belongs to the device record, so you read it from [Business Data Repository](../business-data-repository/README.md).
- The **connectivity** state records whether a device is connected: `ONLINE`, `OFFLINE`, or `NEVER_REPORTED`. Tracking calculates it from the arrival time of the last message and doesn't store it.
- The **motion** state records whether a device is moving, stopped, or parked.

One value can't express all three. A device can be active and offline, and it can be online while inactive.

Four thresholds decide connectivity and motion: a silence period, a speed, a stop duration, and a radius. `workspaceSettings.statusThresholds` returns the values that apply in your workspace. Every workspace uses the same values. A trip history uses the same stop values by default. A vehicle that is parked on the map is therefore at a stop in its history.

## Navigation

The Tracking documentation has two guides and an API reference with one page per concept.

The guides cover the two views that a fleet application builds from Tracking:

- [Building a live fleet map](guides/building-a-live-fleet-map.md) reads the state of every asset in a workspace and places each asset on the map. It then keeps the map current with a state stream over a WebSocket or server-sent events.
- [Reading a vehicle trip history](guides/reading-a-vehicle-trip-history.md) requests the trips and stops of one device for a period, with totals, the route of each trip, and the continuation of a partial result.

The [Tracking API reference](api-reference/README.md) documents the operations and types, one page per concept:

- [Live state](api-reference/state/README.md) documents the state of a device and the state of an asset, the queries that read them, and the status thresholds of a workspace.
- [State streams](api-reference/state/streams.md) documents the subscription that pushes asset state as it changes, and what each message contains.
- [Readings and the field dictionary](api-reference/readings.md) documents how a reading is built and what the dictionary says about it.
- [Trips and stops](api-reference/trips.md) documents the trips and stops of a device, and the settings that control them.

For the types that Tracking shares with the other products, and for an index of the whole API, see the [Core API reference](../core-api-reference/README.md).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
