---
description: Create an IoT Logic flow with an embedded MQTT output node in a single API request. Includes the full request body, curl example, and response structure.
---

# Sending device data to an external system

Let's create a flow that sends your device data to an external system through MQTT. Rather than creating multiple endpoints separately, we can accomplish this in one single request.

{% hint style="info" %}
Need HTTP POST instead of MQTT? Use the [Webhook node](../technical-details/nodes.md#webhook-node-webhook).
{% endhint %}

## Creating a complete flow with integrated MQTT node

The simplest approach is to define both your data sources and MQTT output endpoint directly in your flow creation request. To do it, send a request to the following endpoint:

{% openapi-operation spec="iot-logic" path="/iot/logic/flow/create" method="post" %}
[OpenAPI iot-logic](../resources/api-reference/IoT_Logic.json)
{% endopenapi-operation %}

### Request example

```bash
curl -X POST "https://api.{region}.navixy.com/v2/iot/logic/flow/create" \
  -H "Content-Type: application/json" \
  -H "Authorization: NVX your_token_here" \
  -d '{
  "flow": {
    "title": "Fleet data to external system",
    "enabled": true,
    "nodes": [
      {
        "id": 1,
        "type": "data_source",
        "enabled": true,
        "data": {
          "title": "Fleet vehicles",
          "source_ids": [12345, 12346, 12347]
        },
        "view": {
          "position": { "x": 50, "y": 50 }
        }
      },
      {
        "id": 2,
        "type": "output_endpoint",
        "enabled": true,
        "data": {
          "title": "External MQTT System",
          "output_endpoint_type": "output_mqtt_client",
          "output_endpoint_id": 45678,
          "properties": {
            "protocol": "Navixy Generic Protocol (NGP)",
            "domain": "mqtt.mycompany.com",
            "port": 1883,
            "client_id": "navixy-integration",
            "qos": 1,
            "topics": ["fleet/vehicles/data"],
            "version": "5.0",
            "use_ssl": true,
            "mqtt_auth": true,
            "user_name": "mqtt_username",
            "user_password": "mqtt_password"
          }
        },
        "view": {
          "position": { "x": 250, "y": 50 }
        }
      }
    ],
    "edges": [
      {
        "from": 1,
        "to": 2
      }
    ]
  }
}'
```

The response will include the flow ID:

```json
{
  "success": true,
  "id": 1234
}
```

### Parameters explained

* **Node 1 (`data_source`)**: Collects data from your fleet vehicles.
  * `data.title`: Name of the node. Must be inside the `data` object.
  * `data.source_ids`: Your vehicle IDs. Use each device's `source.id`, not the tracker object ID. See [Source ID vs tracker object ID](../technical-details/nodes.md#source-id-vs-tracker-object-id).
* **Node 2 (`output_endpoint`)**: Sends data to your external MQTT broker.
  * `data.output_endpoint_type: "output_mqtt_client"`: Marks this as an MQTT output endpoint.
  * `data.output_endpoint_id`: A unique identifier you choose for this endpoint.
  * `data.properties.protocol`: The message format, [Navixy Generic Protocol](../Technologies/navixy-generic-protocol/navixy-generic-protocol.md) (NGP).
  * `data.properties.domain` and `port`: Your MQTT broker's address and port. `1883` is the standard MQTT port.
  * `data.properties.client_id`: An identifier for this client connection.
  * `data.properties.qos`: The MQTT quality of service level.
  * `data.properties.topics`: The topics to publish messages to.
  * `data.properties.version`: The MQTT protocol version.
  * `data.properties.use_ssl`: Set to `true` for a secure connection.
  * `data.properties.mqtt_auth`, `user_name`, `user_password`: Set `mqtt_auth` to `true` and provide your broker credentials if the broker requires authentication.
* **Edges**: `{ "from": 1, "to": 2 }` connects the data source node to the MQTT output node.

{% hint style="success" %}
**Congratulations!**

You've now set up a flow that creates a complete end-to-end data pipeline in a single API call. This flow:

* Connects to multiple vehicles in your fleet through a data source endpoint
* Sends the device data to your external MQTT system
* Uses your custom MQTT broker settings for secure data transfer
{% endhint %}
