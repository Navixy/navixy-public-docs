---
description: Call tracker/get_state to retrieve GPS position, speed, connection status, battery, inputs, outputs, and other telemetry data
---

# Get relevant telemetry in one API call (get\_state)

## Question

Which API call returns latest position, speed, status, inputs, outputs, and other telemetry?

## Answer

Use:

`/v2/tracker/get_state`

Example:

`https://api.eu.navixy.com/v2/tracker/get_state`

It returns last known data such as:

* GPS location and time
* speed, heading
* connection status
* movement status
* ignition
* GSM signal
* battery
* inputs / outputs

If you need multiple trackers at once, use `get_states`.

Example response (trimmed to the fields that matter most):

```json
{
  "user_time": "2022-08-31 13:47:13",
  "state": {
    "source_id": 545139,
    "gps": {
      "updated": "2022-08-31 13:47:09",
      "signal_level": 100,
      "location": { "lat": 42.82769, "lng": -78.26290833333333 },
      "heading": 45,
      "speed": 0,
      "alt": 0
    },
    "connection_status": "active",
    "movement_status": "parked",
    "movement_status_update": "2022-08-31 13:40:44",
    "ignition": false,
    "ignition_update": "2022-08-31 13:40:44",
    "gsm": {
      "updated": "2022-08-31 13:47:09",
      "signal_level": 100,
      "network_name": "Mobile",
      "roaming": false
    },
    "last_update": "2022-08-31 13:47:09",
    "battery_level": 97,
    "battery_update": "2022-08-31 13:47:09",
    "inputs": [false, false, false],
    "inputs_update": "2022-08-31 13:47:09",
    "outputs": [true, false],
    "outputs_update": "2022-08-31 13:47:09"
  },
  "success": true
}
```

{% hint style="info" %}
`get_state` returns the state for **one tracker**.

If you need multiple trackers, use [`get_states`](https://www.navixy.com/docs/navixy-api/user-api/backend-api/resources/tracking/tracker#get_states).
{% endhint %}

Note: this method works with a tracker ID. If you need the latest data for more than one tracker, use `get_states`.
