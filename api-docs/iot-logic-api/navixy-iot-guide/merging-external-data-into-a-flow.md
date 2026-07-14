---
description: Configure the Data Source node's connector fields to merge readings from an external system into the data stream of a device already in your flow, with a full request and troubleshooting reference.
---

# Merging external system data into a flow

This guide configures a Data Source node's connector so that data from an external system, one that doesn't use Navixy's native protocols, merges into the stream of a device you already have in your account. The example connects two GPS devices already feeding a flow to a separate battery management system that reports state of charge and temperature for the same vehicles.

For the full field reference, see [Connector configuration](../technical-details/nodes.md#connector-configuration).

## Prerequisites

For this example, let's presume that you already have:

1. An API key with access to the IoT Logic API.
2. A flow with ID `42` containing a Data Source node (ID `1`) that lists two GPS devices, `987654` and `987655`, in `source_ids`.

If you don't remember which devices are assigned to that flow, list them:

{% openapi-operation spec="iot-logic" path="/iot/logic/flow/sources/list" method="get" %}
[OpenAPI iot-logic](../resources/api-reference/IoT_Logic.json)
{% endopenapi-operation %}

```bash
curl -X GET "https://api.eu.navixy.com/v2/iot/logic/flow/sources/list?flow_id=42" \
  -H "Authorization: NVX your_api_key" \
  -H "Content-Type: application/json"
```

```json
{
  "success": true,
  "value": [987654, 987655]
}
```

## Adding the connector to your Data Source node

Update the flow, adding `push_type`, `primary_key`, and `mappings` to the existing Data Source node. `source_ids` stays as is, the connector only adds a second inbound path for the same devices.

{% openapi-operation spec="iot-logic" path="/iot/logic/flow/update" method="post" %}
[OpenAPI iot-logic](../resources/api-reference/IoT_Logic.json)
{% endopenapi-operation %}

```bash
curl -X POST "https://api.eu.navixy.com/v2/iot/logic/flow/update" \
  -H "Authorization: NVX your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "flow": {
      "id": 42,
      "title": "Fleet vehicles with battery management enrichment",
      "enabled": true,
      "nodes": [
        {
          "id": 1,
          "type": "data_source",
          "data": {
            "title": "Fleet vehicles",
            "source_ids": [987654, 987655],
            "push_type": "http",
            "primary_key": "vehicle_id",
            "mappings": [
              { "source_id": 987654, "primary_key_value": "truck_12" },
              { "source_id": 987655, "primary_key_value": "truck_07" }
            ]
          },
          "view": { "position": { "x": 50, "y": 50 } }
        },
        {
          "id": 2,
          "type": "output_endpoint",
          "data": {
            "title": "Send to Navixy",
            "output_endpoint_type": "output_default"
          },
          "view": { "position": { "x": 250, "y": 50 } }
        }
      ],
      "edges": [
        { "from": 1, "to": 2 }
      ]
    }
  }'
```

```json
{
  "success": true,
  "id": 42
}
```

## Getting the push URL

Read the flow back to get the URL Navixy generated for this node:

{% openapi-operation spec="iot-logic" path="/iot/logic/flow/read" method="get" %}
[OpenAPI iot-logic](../resources/api-reference/IoT_Logic.json)
{% endopenapi-operation %}

```bash
curl -X GET "https://api.eu.navixy.com/v2/iot/logic/flow/read?flow_id=42" \
  -H "Authorization: NVX your_api_key" \
  -H "Content-Type: application/json"
```

The Data Source node's `data.url` field now holds the push URL:

```json
{
  "success": true,
  "value": {
    "id": 42,
    "title": "Fleet vehicles with battery management enrichment",
    "enabled": true,
    "nodes": [
      {
        "id": 1,
        "type": "data_source",
        "data": {
          "title": "Fleet vehicles",
          "source_ids": [987654, 987655],
          "push_type": "http",
          "url": "https://api.eu.navixy.com/iot/logic/flow/push?flow_id=42&node_id=1",
          "primary_key": "vehicle_id",
          "mappings": [
            { "source_id": 987654, "primary_key_value": "truck_12" },
            { "source_id": 987655, "primary_key_value": "truck_07" }
          ]
        },
        "view": { "position": { "x": 50, "y": 50 } }
      },
      {
        "id": 2,
        "type": "output_endpoint",
        "data": {
          "title": "Send to Navixy",
          "output_endpoint_type": "output_default"
        },
        "view": { "position": { "x": 250, "y": 50 } }
      }
    ],
    "edges": [
      { "from": 1, "to": 2 }
    ]
  }
}
```

Give this URL to the battery management system, or whatever external system will send the data.

## Sending a push

{% openapi-operation spec="iot-logic" path="/iot/logic/flow/push" method="post" %}
[OpenAPI iot-logic](../resources/api-reference/IoT_Logic.json)
{% endopenapi-operation %}

```bash
curl -X POST "https://api.eu.navixy.com/iot/logic/flow/push" \
  -H "Authorization: NVX your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "flow_id": 42,
    "node_id": 1,
    "vehicle_id": "truck_12",
    "battery_soc": 76,
    "battery_temp": 34.2
  }'
```

```json
{
  "success": true
}
```

{% hint style="warning" %}
A `200` response only confirms that Navixy accepted the request. If `vehicle_id` doesn't match any `mappings[].primary_key_value` on the node, Navixy discards the push without returning an error. Check the mapped device's attributes to confirm the data arrived, for example with [WebSocket access to Data Stream Analyzer](../Websocket-access-for-DSA.md).
{% endhint %}

{% hint style="success" %}
**Congratulations!**

You've now merged an external system's data into an existing device's stream. Device `987654` receives `battery_soc` and `battery_temp` as new attributes alongside its native GPS telemetry, and any downstream node in the flow can reference them like any other attribute.
{% endhint %}

## Handling errors

Navixy validates the connector fields when you save a flow, on `flowCreate` and `flowUpdate`, not when you send a push. A rejected request returns HTTP 400 with a `294` internal code (`IoT Node Invalid`) and a validation error for the affected node.

| Error message | Cause and resolution |
| --- | --- |
| `Data source node ${node} requires a primary key to accept pushes` | `mappings` has entries but `primary_key` is blank. Set `primary_key` to the field name the external system will send. |
| `Data source node ${node} requires at least one device mapping to accept pushes` | `primary_key` is set but `mappings` is empty. Add at least one mapping, or clear `primary_key` for a devices-only node. |
| `Data source node ${node} maps the same device more than once: ${source_id}` | The same `source_id` appears in more than one mapping on this node. Each device needs exactly one mapping. |
| `Data source node ${node} contains a duplicate primary key value: "${primary_key_value}"` | The same `primary_key_value` appears in more than one mapping on this node. Each mapping value must be unique within the node. |
| `Data source node ${node} maps devices that are not in its device list: ${source_ids}` | A mapping points at a device that isn't in this node's `source_ids`. Add the device to `source_ids`, or remove the mapping. |

`${node}` is replaced with the node's title and ID, for example `"Fleet vehicles" (#1)`. `${source_id}`, `${source_ids}`, and `${primary_key_value}` are replaced with the values that caused the error.

### How to handle a rejected flow update

```json
{
  "success": false,
  "status": {
    "code": 294,
    "description": "IoT Node Invalid"
  },
  "errors": [
    {
      "node_ids": [1],
      "message": "Data source node \"Fleet vehicles\" (#1) maps devices that are not in its device list: #991023"
    }
  ]
}
```

Fix the reported node's connector fields and resend the request.

## See also

* [Connector configuration](../technical-details/nodes.md#connector-configuration): full field reference for the connector
* [API reference](../resources/api-reference/): the complete OpenAPI specification
* [WebSocket access to Data Stream Analyzer](../Websocket-access-for-DSA.md): verify that pushed data reached the mapped device
