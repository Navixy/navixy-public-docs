---
description: Build IoT Logic flows from the JSON schema template. Includes the full flow object model with property names, types, and constraints for API-based creation.
---

# JSON-schema template

Here's an example of a JSON structure describing a complete flow.

### Flow object model

{% openapi-schemas spec="iot-logic" schemas="Flow" grouped="true" %}
[OpenAPI iot-logic](../resources/api-reference/IoT_Logic.json)
{% endopenapi-schemas %}

## Example flow

The example template shows a flow that:

1. Collects data from vehicle tracking devices
2. Processes the data in two parallel paths:
   * Calculating fuel-related metrics (level, consumption, range)
   * Calculating engine metrics (temperature, load, maintenance status)
3. Sends the processed data to:
   * The Navixy platform for tracking and visualization
   * An external MQTT broker for integration with other systems

Node-level `title` lives under each node's `data` object, and nodes don't carry their own `enabled` flag. Only the flow itself has a top-level `enabled` flag.

```json
{
  "id": 1001,
  "title": "Vehicle Telematics Processing Flow",
  "enabled": true,
  "nodes": [
    {
      "id": 1,
      "type": "data_source",
      "data": {
        "title": "Vehicle Tracker Input",
        "source_ids": [
          123458,
          123459,
          123460
        ]
      },
      "view": {
        "position": {
          "x": 50,
          "y": 50
        }
      }
    },
    {
      "id": 2,
      "type": "initiate_attributes",
      "data": {
        "title": "Calculate Fuel Metrics",
        "items": [
          {
            "name": "fuel_level_percent",
            "value": "(analog_1 / 1024) * 100",
            "generation_time": "now()",
            "server_time": "now()"
          },
          {
            "name": "fuel_consumption_rate",
            "value": "analog_2 * 0.25",
            "generation_time": "now()",
            "server_time": "now()"
          },
          {
            "name": "estimated_range_km",
            "value": "fuel_level_percent * 5",
            "generation_time": "now()",
            "server_time": "now()"
          }
        ]
      },
      "view": {
        "position": {
          "x": 250,
          "y": 50
        }
      }
    },
    {
      "id": 3,
      "type": "initiate_attributes",
      "data": {
        "title": "Calculate Engine Metrics",
        "items": [
          {
            "name": "engine_temp_celsius",
            "value": "analog_3 * 0.5 - 40",
            "generation_time": "now()",
            "server_time": "now()"
          },
          {
            "name": "engine_load",
            "value": "(analog_4 / 1024) * 100",
            "generation_time": "now()",
            "server_time": "now()"
          },
          {
            "name": "maintenance_due",
            "value": "mileage > 10000",
            "generation_time": "now()",
            "server_time": "now()"
          }
        ]
      },
      "view": {
        "position": {
          "x": 250,
          "y": 200
        }
      }
    },
    {
      "id": 4,
      "type": "output_endpoint",
      "data": {
        "title": "Navixy Platform Output",
        "output_endpoint_type": "output_default"
      },
      "view": {
        "position": {
          "x": 450,
          "y": 125
        }
      }
    },
    {
      "id": 5,
      "type": "output_endpoint",
      "data": {
        "title": "MQTT Broker Output",
        "output_endpoint_type": "output_mqtt_client",
        "output_endpoint_id": 44551
      },
      "view": {
        "position": {
          "x": 450,
          "y": 250
        }
      }
    }
  ],
  "edges": [
    {
      "from": 1,
      "to": 2
    },
    {
      "from": 1,
      "to": 3
    },
    {
      "from": 2,
      "to": 4
    },
    {
      "from": 3,
      "to": 4
    },
    {
      "from": 3,
      "to": 5
    }
  ]
}
```
