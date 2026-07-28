---
description: Create your first IoT Logic flow with a single API request. Builds a four-node flow that detects a speed violation, sends a device command, and forwards data to Navixy.
---

# Quick start: create your first flow

This guide creates a working IoT Logic flow with one API request. The flow collects telemetry from selected devices, checks each message for a speed above 90 km/h, sends a command to the device when the speed is exceeded, and forwards every message to the Navixy platform. Use it to confirm that your credentials work and to see how nodes and edges fit together before you build a flow of your own.

## Prerequisites

Before you send the request, you need three things:

* **An API key or a session hash.** Pass it in the `Authorization` header as `NVX your_hash_or_api_key`. To obtain one, see [Authentication](../authentication.md).
* **The source IDs of the devices that will feed the flow.** Use each device's `source.id`, not the tracker object ID. See [Source ID vs tracker object ID](../technical-details/nodes.md#source-id-vs-tracker-object-id).
* **Your regional base URL**, either `https://api.eu.navixy.com` or `https://api.us.navixy.com`.

## Create the flow

Create a flow with all nodes and connections in a single request:

{% code expandable="true" %}
```bash
curl -X POST "https://api.eu.navixy.com/v2/iot/logic/flow/create" \
  -H "Content-Type: application/json" \
  -H "Authorization: NVX your_hash_or_api_key" \
  -d '{
    "flow": {
      "title": "Speed Violation Alert",
      "enabled": true,
      "nodes": [
        {
          "id": 1,
          "type": "data_source",
          "data": {
            "title": "Fleet Vehicles",
            "source_ids": [111111, 222222, 333333]
          },
          "view": {
            "position": { "x": 50, "y": 250 }
          }
        },
        {
          "id": 2,
          "type": "logic",
          "data": {
            "title": "Speed > 90 km/h?",
            "name": "speed_violation",
            "condition": "speed > 90"
          },
          "view": {
            "position": { "x": 320, "y": 250 }
          }
        },
        {
          "id": 3,
          "type": "action",
          "data": {
            "title": "Trigger In-Cab Buzzer",
            "actions": [
              {
                "type": "send_gprs_command",
                "command": "setdigout 1 1",
                "reliable": true
              }
            ]
          },
          "view": {
            "position": { "x": 590, "y": 100 }
          }
        },
        {
          "id": 4,
          "type": "output_endpoint",
          "data": {
            "title": "Send to Navixy",
            "output_endpoint_type": "output_default"
          },
          "view": {
            "position": { "x": 590, "y": 400 }
          }
        }
      ],
      "edges": [
        { "from": 1, "to": 2, "type": "simple_edge" },
        { "from": 2, "to": 3, "type": "then_edge" },
        { "from": 2, "to": 4, "type": "then_edge" },
        { "from": 2, "to": 4, "type": "else_edge" }
      ]
    }
  }'
```
{% endcode %}

Response (example):

```json
{
  "success": true,
  "id": 123
}
```

## Parameters explained

* **Flow entity**: The main container defining a complete data processing pipeline
  * `title`: Names your flow for easier identification
  * `enabled`: When true, flow begins processing data immediately after creation
* **Nodes**: Functional components that each handle a specific step in data processing. See [Nodes](../technical-details/nodes.md) for full node schemas and options.
  * **Node 1 (`data_source`)**: Entry point for device telemetry.
    * `source_ids`: Which devices feed messages into this flow. Use each device's `source.id`, not the tracker object ID. See [Source ID vs tracker object ID](../technical-details/nodes.md#source-id-vs-tracker-object-id).
  * **Node 2 (`logic`)**: Branching decision based on a boolean expression.
    * `condition: "speed > 90"` routes each message to THEN or ELSE.
  * **Node 3 (`action`)**: Executes device commands on the THEN branch.
    * `actions[].type: "send_gprs_command"` sends the command to the triggering device (by default).
  * **Node 4 (`output_endpoint`)**: Terminates the flow and defines the destination.
    * `output_endpoint_type: "output_default"` sends messages to Navixy.
* **Edges**: Define connections between nodes (the data path).
  * `{ "from": 1, "to": 2 }`: data source → logic.
  * `{ "from": 2, "to": 3, "type": "then_edge" }`: violation → action.
  * `{ "from": 2, "to": 4, "type": "then_edge" }`: violation → output.
  * `{ "from": 2, "to": 4, "type": "else_edge" }`: no violation (or condition can’t be evaluated) → output.
  * Both Logic branches resolve to an output endpoint (required for a valid flow).

## What this flow does

{% hint style="success" %}
This single request creates a complete flow that:

* Collects data from `source_ids: [111111, 222222, 333333]`
* Routes messages by speed condition `speed > 90`
* Triggers `send_gprs_command` on the THEN branch
* Outputs messages via `output_default`

The success response includes the ID of the newly created flow, which you can use for future operations like updating the flow or adding additional nodes.

You can expand this example by adding more devices, creating additional calculated attributes, or configuring MQTT endpoints to send data to external systems.
{% endhint %}

## Next steps

* [Sending device data to an external system](scenario1.md): send the same flow's output to your own MQTT broker instead of to Navixy.
* [Managing your flows and endpoints](scenario2.md): list, read, and update the flow you just created.
* [Nodes](../technical-details/nodes.md): the full schema and options for every node type used in this example.
* [Technical reference](../technical-details/): rate limits, error codes, and flow validation rules.
