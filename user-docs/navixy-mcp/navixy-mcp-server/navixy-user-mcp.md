# Navixy User MCP (navixy-user)

Navixy User MCP connects your AI client to your Navixy user account. Use it to monitor your fleet, check sensor readings, review track history, inspect IoT Logic flows, and visualize live tracker positions — all from the AI client you already work in.

This page describes what Navixy User MCP can do and provides a full reference for all available tools (operations your AI client can perform), including parameter names and example prompts for developers building integrations. If you haven't set up the connection yet, refer to [Getting started](getting-started/).

**Endpoint:** `https://platform.mcp.navixy.com/user/mcp`

**See also:**

* [Troubleshooting](../troubleshooting.md)

## What you can do with Navixy User MCP

### Monitor your fleet

Get the current status of all your trackers or a specific set of them. Your AI client can tell you which trackers are online, moving, parked, or offline, along with their last known position, speed, heading, ignition state, and battery level. You can ask for all trackers at once or filter by group or tag.

Example prompts:

```
Which of my trackers are online right now?
```

```
Show me all moving vehicles and their current speed.
```

### Look up a specific tracker or vehicle

You can refer to any tracker by name rather than by ID. Navixy User MCP uses fuzzy matching, so approximate names work. "Fiat" will find "Fiat Fiorino," and "Volvo" will find "Volvo Truck."

For a named tracker, your AI client can retrieve its current state (position, movement status, ignition) or its latest sensor readings (voltage, fuel level, temperature, odometer, engine hours).

Example prompts:

```
What is the current status of the Volvo Truck?
```

```
Show me the latest sensor readings for the Fiat Fiorino.
```

### View track history

Your AI client can retrieve the movement history of any tracker for a specified time period. The response lists individual track segments with start point, end point, distance, and duration for each segment. A single day of driving typically returns multiple segments separated by stops.

Example prompts:

```
Show me the tracks for the Volvo Truck from last Monday.
```

```
Where did the Fiat Fiorino go between 8am and 6pm yesterday?
```

### Organize by groups and tags

Your AI client can list all tracker groups and tags in your account, search for a specific group or tag by name, and filter the tracker list by group or tag. Filtering supports both "match all tags" and "match at least one tag" logic.

Example prompts:

```
List all my tracker groups.
```

```
Show me all trackers tagged as "refrigerated."
```

### Work with vehicles

Your AI client can retrieve the full vehicle list with metadata, including registration number, vehicle type, fuel type, and average fuel consumption. It can also find a specific vehicle by name using fuzzy matching.

Example prompts:

```
List all vehicles and their registration numbers.
```

```
What fuel type does the Honda Forza use?
```

### Work with IoT Logic flows

Your AI client can list all IoT Logic flows in your account, retrieve details and configuration of a specific flow, export a flow definition, list all endpoints across all flows, and show which devices are assigned to which flows.

This is particularly useful for auditing your IoT Logic setup or checking source mappings across a large number of devices.

Example prompts:

```
List all my IoT Logic flows and tell me which ones are enabled.
```

```
Which devices are assigned to the "Demo" flow?
```

### Visualize the tracker on the map

{% hint style="warning" %}
Map visualization requires an AI client that supports MCP Apps. Not all AI clients support visualization.
{% endhint %}

Your AI client can display an interactive live map of all your trackers, grouped by movement status (moving, parked, offline), with automatic updates every five seconds.

Example prompt:

```
Show me my trackers on a map.
```

<figure><img src="../.gitbook/assets/image (2).png" alt="Navixy Tracker Map rendered in Claude Desktop, showing moving, parked, and offline trackers across Europe. The map updates automatically every five seconds and groups trackers by movement status" width="563"><figcaption><p>Navixy tracker map</p></figcaption></figure>

## Tools reference

### Trackers

<table><thead><tr><th width="151.79998779296875">Tool</th><th width="205.20001220703125">What it does</th><th width="241.60003662109375">Parameters</th><th>Example prompt</th></tr></thead><tbody><tr><td><code>tracker_list</code></td><td>Retrieves all trackers with metadata. Optionally filters by group or tag.</td><td><code>groupId</code> (optional, -1 or omitted means no filtering), <code>tagIds</code> (optional, empty means no filtering), <code>atLeastOneTagIdMatch</code> (optional, default false)</td><td>"List all my trackers."</td></tr><tr><td><code>tracker_states</code></td><td>Returns the current state of one or more trackers: position, speed, movement status, ignition, inputs, battery. Pass empty array for all trackers.</td><td><code>trackerIds</code> (required, empty array means all)</td><td>"Which trackers are currently moving?"</td></tr><tr><td><code>tracker_state_by_label</code></td><td>Returns the current state of a single tracker found by fuzzy name match.</td><td><code>trackerLabelQuery</code> (required)</td><td>"What is the current status of the Volvo Truck?"</td></tr><tr><td><code>tracker_readings</code></td><td>Returns the latest sensor readings for one or more trackers: inputs, counters, virtual sensors. Pass empty array for all trackers.</td><td><code>trackerIds</code> (required, empty array means all)</td><td>"Show me fuel levels for all trackers."</td></tr><tr><td><code>tracker_readings_by_label</code></td><td>Returns the latest sensor readings for a single tracker found by fuzzy name match.</td><td><code>trackerLabelQuery</code> (required)</td><td>"What is the odometer reading for the Fiat Fiorino?"</td></tr><tr><td><code>tracker_track_list</code></td><td>Returns the movement history of a tracker for a given time period.</td><td><code>tracker</code> (required, tracker ID), <code>from</code> (required, <code>yyyy-MM-ddTHH:mm:ss</code>), <code>to</code> (required, <code>yyyy-MM-ddTHH:mm:ss</code>)</td><td>"Show me the Volvo Truck's tracks from yesterday."</td></tr></tbody></table>

### Groups and tags

| Tool                     | What it does                               | Parameters              | Example prompt                      |
| ------------------------ | ------------------------------------------ | ----------------------- | ----------------------------------- |
| `tracker_group_list`     | Returns all tracker groups in the account. | None                    | "List all my tracker groups."       |
| `tracker_group_by_title` | Finds a tracker group by fuzzy name match. | `labelQuery` (required) | "Find the group called Delivery."   |
| `tag_list`               | Returns all tags in the account.           | None                    | "What tags do I have?"              |
| `tag_by_name`            | Finds a tag by fuzzy name match.           | `labelQuery` (required) | "Find the tag called refrigerated." |

### Vehicles

| Tool               | What it does                                                                                                | Parameters              | Example prompt                                      |
| ------------------ | ----------------------------------------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------- |
| `vehicle_list`     | Returns all vehicles with metadata including type, fuel type, registration number, and average consumption. | None                    | "List all vehicles and their registration numbers." |
| `vehicle_by_label` | Finds a vehicle by fuzzy name match.                                                                        | `labelQuery` (required) | "What fuel type does the Honda Forza use?"          |

### IoT Logic

| Tool                             | What it does                                                  | Parameters                                                        | Example prompt                                              |
| -------------------------------- | ------------------------------------------------------------- | ----------------------------------------------------------------- | ----------------------------------------------------------- |
| `iot_logic_flow_list`            | Lists all IoT Logic flows with their status and device count. | None                                                              | "List all my IoT Logic flows."                              |
| `iot_logic_flow_read`            | Returns the full configuration of a specific flow.            | `flow_id` (required)                                              | "Show me the configuration of the Demo flow."               |
| `iot_logic_flow_export`          | Exports a flow definition as a reusable template.             | `flow_id` (required)                                              | "Export the Demo flow."                                     |
| `iot_logic_endpoint_list`        | Lists all input and output endpoints across all flows.        | None                                                              | "What endpoints do I have in IoT Logic?"                    |
| `iot_logic_endpoint_read`        | Returns the detailed configuration of a specific endpoint.    | `endpoint_id` (required)                                          | "Show me the configuration of the Default Output Endpoint." |
| `iot_logic_sources_mapping_list` | Lists all device-to-flow assignments. Supports pagination.    | `limit` (optional, default 10000), `offset` (optional, default 0) | "Which devices are assigned to the Demo flow?"              |

### Geocoding

| Tool                         | What it does                                          | Parameters                         | Example prompt                                                |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------- |
| `search_address_by_location` | Converts GPS coordinates to a human-readable address. | `lat` (required), `lng` (required) | "What is the address of the Fiat Fiorino's current location?" |

### Visualization

| Tool               | What it does                                                                                             | Parameters                                | Example prompt                  |
| ------------------ | -------------------------------------------------------------------------------------------------------- | ----------------------------------------- | ------------------------------- |
| `show_tracker_map` | Displays an interactive live map of all trackers grouped by movement status. Updates every five seconds. | `groupId` (optional), `tagIds` (optional) | "Show me my trackers on a map." |

### Utility

| Tool                   | What it does                                                                                        | Parameters                                                                             | Example prompt                            |
| ---------------------- | --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ----------------------------------------- |
| `get_current_datetime` | Returns the current date and time in the user's account timezone. Supports multiple output formats. | `dateTimeFormat` (required: `date`, `time`, `iso`, `datetime`, or custom Luxon format) | "What time is it in my account timezone?" |

{% hint style="info" %}
`get_current_datetime` returns a formatted string in the user's account timezone, not the local system time. The format depends on the `dateTimeFormat` parameter: `iso` returns a full ISO 8601 timestamp, `date` returns the date only, `time` returns the time only.
{% endhint %}
