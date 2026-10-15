---
description: >-
  How IoT Logic flows work in Navixy GraphQL API: how a message moves through
  the graph, where calculated attributes go, and how a write replaces a flow.
tags:
  - fundamentals
  - beta
---

# How IoT Logic flows work

This page explains the rules that every IoT Logic flow follows, whether you build it on the canvas or through the API. It covers how a device message moves through the graph, where the calculated attributes go, and what a create or update request does to a stored flow. It is for developers who build flows through Navixy GraphQL API. For the terms that it uses, such as flow node and transition, see [Key concepts](README.md#key-concepts).

## How a message moves through a flow

A flow runs once for every message of a device in its data sources. The message enters at the data source and follows the transitions from node to node. A path is active for a message when the message follows it. These rules decide what a graph can do:

* Only a path that reaches an Output Endpoint keeps the attributes that it calculated. They reach their destination only through an Output Endpoint node. A path that ends at any other node keeps nothing. The message still reaches the Navixy platform, with only the values that the device sent.
* Both outcomes of a logic node are real paths. The message continues on the `THEN` transition or on the `ELSE` transition, and each one can lead to its own nodes.
* One node can start several paths. A node can have several outgoing transitions of the same type, and the message follows all of them. Only one of these paths keeps its attributes, as the next rule explains. To calculate more values, add the nodes one after another on one path.
* Only one Output Endpoint keeps attributes for each message. When two paths are active for the same message and each one reaches its own Output Endpoint, Navixy keeps the attributes of only one of them. To keep every attribute, calculate them on one path, or end the paths in the `THEN` and `ELSE` outcomes of one logic node, which are never active together.
* Paths never join again. Two paths that are active for the same message can't lead into the same node, because the attributes of one path would be lost. A write with such a graph fails with `SIMULTANEOUS_PATHS`. The `THEN` and `ELSE` outcomes of one logic node are never both active for the same message, so they can lead into the same node.

A later node on a path can read every attribute that an earlier node on the same path calculated.

## Where calculated attributes appear

An Output Endpoint node sends the message to the Navixy platform. The attributes that the flow calculated arrive together with the values that the device sent. The platform stores them in the history of the device. In a later message of the same device, an expression of the same flow can read them with `value()`. Other flows can't read them.

To see the values that arrive, read them with the `flowDebugValues` query. It returns the last 12 values of every attribute of up to 10 devices of a flow, as the messages leave the flow, including the attributes that a device model reports under its own names. See [Checking what a flow calculated](guides/reading-flows-and-device-data.md#checking-what-a-flow-calculated). In the flow editor, the same values appear in Data Stream Analyzer. To list only the attribute names of your devices, without values, use the `deviceAttributes` query. See [Find the attribute names of your devices](guides/reading-flows-and-device-data.md#find-the-attribute-names-of-your-devices).

## How a write changes a flow

Four rules apply to every flow that you create or update. They are the rules that differ most from the other products of Navixy GraphQL API:

* Every write sends the whole graph. Send the `nodes` and `transitions` lists of an update together: they replace the stored graph. A node that the lists leave out is removed, together with its devices. An update with `nodes` and without `transitions` keeps the stored transitions.
* Transitions refer to nodes by key. Navixy creates the ID of a new node, so you can't know it before the write. Give every node a `key`, and use the keys in `from` and `to`. To keep a stored node, send its `id` next to its key.
* Devices stay unless you change them. In a data source node of an update, leave out `deviceIds` to keep its current devices, or send `deviceIds: []` to remove all of them.
* Validation returns every problem at once. A write that breaks a rule fails with a list of violations, and each one names the input field to fix. See [Flow validation errors](flow-validation-errors.md).

A new flow is always disabled. Enabling it validates the whole graph again, and disabling it validates nothing. See [When Navixy validates a flow](flow-validation-errors.md#when-navixy-validates-a-flow). For every step with a request and a response, see [Creating and updating flows](guides/creating-and-updating-flows.md).

## Devices in a flow

A flow doesn't return its device list with the flow, because one flow can process up to 25,000 devices. Read `deviceCount` for the number of devices. To see the devices themselves, read `devices` on each data source node.

`deviceCount` counts a device once for each data source that lists it, so a device in two data sources of one flow counts twice, as it appears in both `devices` lists. In rare cases, a flow keeps a device that no longer exists: `deviceCount` still counts it, and `devices` leaves it out.

To find the flows that a device is in, use `deviceFlowLinks`. It lists every device of every flow in your workspace, with the flow and the data source. See [Finding the flows of a device](guides/reading-flows-and-device-data.md#finding-the-flows-of-a-device).

In a workspace that has IoT Logic, a device can't be deleted while a flow lists it in a data source. Every flow that isn't deleted counts, whether it's enabled or not. `deviceDelete` then returns [`IN_USE`](../error-handling.md#in-use-409), and the error lists the IDs of up to 20 of these flows, only the flows that your roles let you read. Remove the device from each data source that lists it, or delete the flow, then delete the device. See [Deleting a device](../business-data-repository/guides/managing-device-records-and-identifiers.md#deleting-a-device). `flowDelete` succeeds even while the flow lists devices.

## See also

* [IoT Logic node types](node-types.md): Look up the fields and rules of each node type.
* [Flow validation errors](flow-validation-errors.md): Find the violation code that a broken rule returns, and fix it.
* [Creating and updating flows](guides/creating-and-updating-flows.md): Apply these rules in a complete flow, step by step.
