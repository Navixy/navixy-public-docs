---
description: >-
  IoT Logic in Navixy GraphQL API: create, read, update, enable, and delete the
  flows that process the messages of your devices.
tags:
  - overview
  - beta
---

# IoT Logic overview

**IoT Logic** is the product that processes the messages of your devices as they arrive. It is one of the products served by [Navixy GraphQL API](../). A flow reads each message of the devices attached to it, calculates new values, and adds them to the message. The same flows appear on the canvas of the IoT Logic flow editor, so a flow that you create through the API opens in the editor, and the reverse.

All IoT Logic operations are nested under the `iotLogic` field of the `Query` and `Mutation` types. Every operation acts in the workspace that your access token names, so no query or mutation takes a workspace ID. The shared pages of this space cover the request format, [authentication](../authentication.md), [pagination](../pagination.md), and [error handling](../error-handling.md). For your first request, see [Getting started](../getting-started.md).

## Purpose and capabilities

IoT Logic enables you to:

* Create a flow from a graph of nodes and the transitions between them
* Read one flow, or list the flows of your workspace with filters and sorting
* Replace the graph of a flow, change its title and description, and enable or disable it
* Attach devices to a flow, see how many devices it processes, and find the flows that a device is in
* Send commands to devices from a flow, such as switching an output on when a condition is true
* List the attribute names that your devices report, to write expressions with them
* Read the recent values of the devices of a flow, to check what the flow calculated
* Delete a flow that you no longer need

## Key concepts

<table><thead><tr><th width="160">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Flow</strong></td><td>A graph that processes the messages of the devices attached to it. A flow is created disabled. It processes messages only after you enable it.</td></tr><tr><td><strong>Flow node</strong></td><td>One step of a flow, shown as a box on the canvas. Each node has a type, such as a data source or a logic check. A flow node isn't a GraphQL <a href="../core-api-reference/common.md#node"><code>Node</code></a>: you read it only through its flow.</td></tr><tr><td><strong>Transition</strong></td><td>A directed connection from one node to another. A transition has a type: <code>SIMPLE</code>, or <code>THEN</code> and <code>ELSE</code> for the two outcomes of a logic node.</td></tr><tr><td><strong>Data source</strong></td><td>The node where messages enter a flow. It holds the list of devices that the flow processes. A flow can have several data sources.</td></tr><tr><td><strong>Attribute</strong></td><td>A named value in a device message, such as <code>temperature</code>. Nodes calculate new attributes with expressions and add them to the message. A later node can read an attribute that an earlier node calculated.</td></tr><tr><td><strong>Expression</strong></td><td>A formula in the Navixy IoT Logic Expression Language, such as <code>temperature > 8</code>. Logic nodes use one as a condition, and Initiate Attribute nodes use one to calculate each attribute.</td></tr><tr><td><strong>Node key</strong></td><td>A name that you give each node in a write request, such as <code>check</code>. Transitions in the same request refer to nodes by key. A key is valid only inside that one request.</td></tr></tbody></table>

## Key workflow

A flow goes through the same steps whether you build it on the canvas or through the API:

1. Create the flow with its whole graph: the nodes, the transitions between them, and the devices of each data source. The flow starts disabled.
2. Read the flow back to get the IDs that Navixy created for the flow and its nodes.
3. Change the flow with an update that sends the whole new graph.
4. Enable the flow. From then on, it processes every message of its devices.
5. Check the recent values of its devices to confirm what the flow calculated.
6. Disable or delete the flow when you no longer need it.

For the rules behind each step, see [How IoT Logic flows work](how-flows-work.md). For every step with a request and a response, see [Creating and updating flows](guides/creating-and-updating-flows.md).

## Who does what

Three parties share the work on the flows of a workspace:

<table><thead><tr><th width="200">Who</th><th>Does</th></tr></thead><tbody><tr><td><strong>You</strong></td><td>Create and change flows, choose the devices of each data source, enable and disable flows, and read the values that a flow calculated. Every call acts in the workspace named by the token.</td></tr><tr><td><strong>Business Data Repository</strong></td><td>Stores the devices that the data sources of a flow list. A flow refers to them by device ID.</td></tr><tr><td><strong>Navixy Console</strong></td><td>Gives you your roles. An administrator of your organization assigns them.</td></tr></tbody></table>

Your roles decide what you can do with flows. Reading flows needs the `read` action on IoT Logic flows, and `flowCreate`, `flowUpdate`, and `flowDelete` need the `create`, `update`, and `delete` actions. Without the action, the operation fails with `PERMISSION_DENIED`.

The devices of a flow follow your access to devices in Business Data Repository. A flow shows, counts, and changes only the devices that your roles let you read. The other devices stay in the flow. See [What your roles and products allow](../authentication.md#what-your-roles-and-products-allow).

## Navigation

The IoT Logic documentation has three sections: concepts, guides, and the API reference.

### Concepts

Pages that explain how flows behave and what each part of a flow accepts:

* [How IoT Logic flows work](how-flows-work.md): How a message moves through a flow, where calculated attributes go, and what a write does to a stored flow
* [IoT Logic node types](node-types.md): The fields, the behavior, and the rules of each node type
* [Flow validation errors](flow-validation-errors.md): The structure of a validation error and every violation code, with its fix

### Guides

Step-by-step walkthroughs of the most common scenarios:

* [Creating and updating flows](guides/creating-and-updating-flows.md)
* [Reading flows and device data](guides/reading-flows-and-device-data.md)
* [Calculating values in several steps](guides/calculating-values-in-several-steps.md)

### API reference

The [IoT Logic API reference](api-reference/README.md) gives the complete technical specification for the GraphQL types and operations of IoT Logic:

* [Flows](api-reference/flows.md)

For the types that IoT Logic shares with the other products, and for an index of the whole API, see the [Core API reference](../core-api-reference/README.md).

{% hint style="warning" %}
The API supports [GraphQL introspection](../graphql-basics/#introspection) for authenticated users. You can also check the public [GraphQL schema](../developer-resources/public-graphql-schema.md).
{% endhint %}
