---
description: >-
  How the Tracking reference is organized: one page per concept for live state,
  state streams, readings and the field dictionary, and trips and stops.
---

# Tracking API reference

{% include "../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

The Tracking API reference documents every operation and type of Tracking, grouped into one page per concept. Each page covers its queries or subscriptions, its object and input types, and its enums, with a field table for every type.

The reference states what each operation and field is. For how to use them together, see the [Tracking guides](../guides/README.md), and for the GraphQL fundamentals behind the notation, see [GraphQL basics](../../graphql-basics/), including the [type syntax](../../graphql-basics/#type-syntax) that the field tables use.

## Where the operations live

All Tracking queries are nested under the `tracking` field of the `Query` type. The two state streams are fields of the `Subscription` type, at the root, because a subscription cannot be nested. Business Data Repository owns the asset and device records that Tracking reports on, so an asset state names an asset from Business Data Repository and a device state names a device from it.

## Pages

<table><thead><tr><th width="240">Page</th><th>Covers</th></tr></thead><tbody><tr><td><a href="state/">Live state</a></td><td>The last known position, connectivity, motion, readings, and events of a device (<code>TrackingDeviceState</code>), the per-device states of an asset (<code>TrackingAssetState</code>), and the queries that read them: <code>deviceState</code>, <code>deviceStates</code>, <code>assetState</code>, and <code>assetStates</code> with its filter and order inputs.</td></tr><tr><td><a href="state/streams.md">State streams</a></td><td>The subscriptions that push the full state of an asset or a device on every report, delivered in batches, with the opening-snapshot markers and the removal signals that each message can contain.</td></tr><tr><td><a href="readings.md">Readings and the field dictionary</a></td><td>The value model of a reading (<code>TrackingReading</code> and its typed values), the unit rules, and the field dictionary with the queries that return it: <code>hotFieldCodes</code> and <code>fieldDefinitions</code>.</td></tr><tr><td><a href="trips.md">Trips and stops</a></td><td>The timelines that Tracking calculates from the message archive (<code>deviceTimeline</code> and <code>assetTimeline</code>), the segmentation policy that cuts them into trips and stops, and the options that shape the returned points.</td></tr></tbody></table>

## Shared types

The scalars, interfaces, and pagination types that Tracking shares with the other products are on the [Common resources](../../core-api-reference/common.md) page. The [Core API reference](../../core-api-reference/README.md) indexes the reference pages of every product.

## How the reference pages are structured

Every page follows the same order: queries or subscriptions, then the page's objects, inputs, and enums, with pagination types in a section of their own at the end. Field tables use the GraphQL type notation, where `!` marks a value that is never null and `[...]` marks a list: see [Type syntax](../../graphql-basics/#type-syntax).

## Other ways to explore the API

* [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox): Run queries and mutations interactively.
* [Public GraphQL schema](../../developer-resources/public-graphql-schema.md): The full type system split into several files.
* [Introspection](../../graphql-basics/#introspection): Explore the schema from your own GraphQL client.

## See also

* [Tracking overview](../overview.md): What Tracking covers and how its concepts fit together
* [Tracking guides](../guides/README.md): A live fleet map and a vehicle trip history, built from these operations
* [Error handling](../../error-handling.md): Understand error structure, codes, and common error scenarios
