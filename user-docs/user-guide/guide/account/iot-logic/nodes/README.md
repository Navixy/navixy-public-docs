---
description: Browse all IoT Logic node types, including Data Source, Initiate Attribute, IF/THEN Logic, Device action, Output Endpoint, and Webhook. Links to each guide.
---

# Nodes

Nodes are the building blocks of an IoT Logic flow. Each node handles a specific stage of the data lifecycle, from receiving raw device telemetry to forwarding enriched data to its destination. Every flow requires at minimum one input node and one output node. This page is a reference overview of all available node types, follow the links in the table for full configuration details.

The **Nodes** pane on the canvas groups these node types into four labeled sections, in the order you'd typically use them in a flow: **Start with** and **End with** are required, **Process** and **Action** are optional.

<table><thead><tr><th width="161">Node</th><th width="141">Panel group</th><th>Description</th></tr></thead><tbody><tr><td><a href="data-source-node.md">Data Source</a></td><td>Start with (required)</td><td>Connects devices to the flow and receives their telemetry data as the entry point for all data processing <em>(input)</em>. Can also enrich an already-connected device with attributes pushed from an external system.</td></tr><tr><td><a href="initiate-attribute-node/">Initiate Attribute</a></td><td>Process (optional)</td><td>Creates calculated attributes from incoming device parameters <em>(enrichment)</em> using the <a href="https://app.gitbook.com/s/tx3J5BxnWyPV0nP2xr0z/technologies/navixy-iot-logic-expression-language">Navixy Expression Language</a>.</td></tr><tr><td><a href="logic-node/">IF/THEN Logic</a></td><td>Process (optional)</td><td>Routes data through conditional branches <em>(routing)</em> based on logical expressions evaluated against incoming values.</td></tr><tr><td><a href="action-node.md">Device action</a></td><td>Action (optional)</td><td>Sends commands back to devices when triggered by incoming data <em>(action)</em>, with a possibility to map actions between trigger and target devices.</td></tr><tr><td><a href="webhook-node.md">Webhook</a></td><td>Action (optional)</td><td>Sends HTTP POST requests to external systems when triggered by incoming data <em>(action)</em>, and allows outbound integrations with 3rd-party software.</td></tr><tr><td><a href="output-endpoint-node.md">Output Endpoint</a></td><td>End with (required)</td><td>Transmits processed data <em>(output)</em> to the Navixy platform or third-party systems via MQTT.</td></tr></tbody></table>

{% hint style="info" %}
**Device action** and **Webhook** are terminal nodes, they perform an action when triggered but don't pass data forward, so no outgoing connections can be added after them. If you place a terminal node on a conditional branch of an **IF/THEN Logic** node, connect the Logic node directly to an **Output Endpoint** node in parallel to ensure data continues flowing through the other branch.
{% endhint %}
