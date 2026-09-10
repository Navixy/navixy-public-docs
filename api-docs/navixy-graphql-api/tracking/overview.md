---
description: >-
  Tracking serves the live state of assets and their devices, the streams that
  follow that state, and the trips and stops derived from device messages.
---

# Tracking overview

{% include "../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

**Tracking** is the product that answers where your assets are and what their devices report. It is one of the products served by [Navixy GraphQL API](../). [Business Data Repository](../business-data-repository/overview.md) holds the records of an asset and of the devices linked to it. Tracking holds what those devices report: position, connection, motion, sensor readings, and the trips and stops that follow from them.

All Tracking queries are nested under the `tracking` field of the `Query` type. The two state streams are fields of the `Subscription` type. The shared pages of this space cover the request format, [authentication](../authentication.md), [pagination](../pagination.md), and [error handling](../error-handling.md). For the endpoint and the headers of a request, see [Getting started](../getting-started.md).

## Purpose and capabilities

Tracking enables you to:

- Read the current state of one asset, of every asset in a workspace, or of a single device
- Follow that state as it changes, without polling for it
- Read the trips and stops of an asset or a device over a period of time
- Read the dictionary that gives every device reading a title, a unit, and a kind

## Key concepts

<table><thead><tr><th width="147.48895263671875">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Device state</strong></td><td>The last known picture of one device: where it is, whether it is reporting, whether it is moving, and what its sensors last sent.</td></tr><tr><td><strong>Asset state</strong></td><td>The states of the devices linked to one asset, side by side. Tracking doesn't merge them, because two devices on one asset can report the same value differently.</td></tr><tr><td><strong>Reading</strong></td><td>A named value that a device sent, such as an ignition state or a fuel level. Every reading carries the moment it arrived, so the readings of one state can differ in age.</td></tr><tr><td><strong>Field dictionary</strong></td><td>The catalog of reading names. It gives a name a title, a unit, and a kind, so a client can render a reading it has never seen.</td></tr><tr><td><strong>Stream</strong></td><td>A subscription that pushes the whole state of a followed asset or device every time it reports. A client replaces its copy of the state and keeps no update logic of its own.</td></tr><tr><td><strong>Trip</strong></td><td>A segment of movement between two stops. Trips aren't stored: Tracking derives them from the messages of a device when you ask for them.</td></tr><tr><td><strong>Stop</strong></td><td>A period during which a device stayed in one place. Derived on request, in the same way as a trip.</td></tr></tbody></table>

## Device states

A device has three states, and each one changes on its own:

- The **lifecycle** state records whether a device is registered and active. It belongs to the device record, so you read it from [Business Data Repository](../business-data-repository/overview.md).
- The **connection** state records whether a device is reporting. Tracking computes it from the time of the last message and doesn't store it.
- The **motion** state records whether a device is moving or stopped.

One scale can't hold all three. A device can be active and silent, and it can be reporting while inactive.

## Navigation

The Tracking documentation is an API reference with one page per concept.

- [Live state](api-reference/state/README.md) documents the state of a device and the state of an asset, with the queries that read them.
- [State streams](api-reference/state/streams.md) documents the subscriptions that push state as it changes, and what each message carries.
- [Readings and the field dictionary](api-reference/readings.md) documents how a reading is built and what the dictionary says about it.
- [Trips and stops](api-reference/trips.md) documents the trips and stops of a device, and the settings that shape them.

For the types that Tracking shares with the other products, and for an index of the whole API, see the [Core API reference](../core-api-reference/README.md).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
