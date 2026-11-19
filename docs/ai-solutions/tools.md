---
hidden: true
noIndex: true
description: >-
  Every tool of Navixy MCP Server with its parameters and an example prompt, for
  developers who build integrations or check which tool an AI client used.
tags:
  - reference
  - beta
---

# Navixy MCP Server tools

Navixy MCP Server gives your AI client tools: operations that the AI client performs in Navixy. The client chooses the tools itself, so you don't need this page to work with it. The page lists every tool with its parameters for developers who build integrations or want to know which tool the AI client used. To learn what you can ask, see [Navixy MCP Server capabilities](capabilities.md).

A tool that reads one asset, device, or geo object needs its ID. The AI client can find the ID with a search tool or `execute_query`. Times are date-times with an offset, such as `2026-10-20T00:00:00Z`.

Every tool that changes, deletes, or activates something also requires `operationId`, an ID that the AI client chooses for the change. When the client repeats a request with the same `operationId`, Navixy doesn't make the change twice. A tool that changes or deletes something requires its `version` to avoid overwriting a change made by someone else.

## Workspace and search

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `get_workspace` | Returns the name and ID of the connected workspace. | None | "Which Navixy workspace are you connected to?" |
| `search_assets` | Finds assets whose title or license plate contains a text. | `text` (required, 2 to 100 characters) | "Find the Volvo trucks." |
| `search_asset_groups` | Finds up to 20 asset groups whose title contains a text. | `text` (required, 2 to 100 characters) | "Which asset groups have North in the name?" |
| `search_geo_objects` | Finds up to 20 geo objects whose title contains a text. | `text` (required, 2 to 100 characters) | "Find the depot geo objects." |
| `list_asset_types` | Lists the workspace's asset types with their fields. | None | "Which fields does a vehicle asset have?" |

## Positions and current readings

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `locate_assets` | Returns the last known position, motion status, and connection status of assets and their devices, 100 assets at a time. | `assetIds`, `groupIds`, `motionStatus`, `connectivityStatus`, `after` (all optional) | "Which vehicles are moving right now?" |
| `locate_devices` | Returns the same data for devices. | `deviceIds` (required, up to 100) | "When did the GPS device of Volvo FH 4521 last report?" |
| `get_current_readings` | Returns the newest value of each sensor reading of the assets' devices: fuel, temperature, ignition, voltage, odometer, and others. | `assetIds` (required, up to 20), `codes` (optional) | "What is the fuel level of Volvo FH 4521 right now?" |

## Trips and history

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `list_trips` | Lists an asset's trips and stops over a period, with distance, driving time, and stop time. | `assetId`, `from`, `to` (all required) | "How far did Volvo FH 4521 drive last week?" |
| `get_timeline` | Returns the route of an asset or a device over a period, with up to 50 points for each trip. | `from`, `to` (required), `assetId` or `deviceId` (one is required) | "Show me the route of Volvo FH 4521 from yesterday." |
| `list_safety_events` | Lists the harsh acceleration, harsh braking, harsh turns, and SOS alarms that the asset's devices reported over a period. | `assetId`, `from`, `to` (all required) | "Any harsh braking on Volvo FH 4521 this month?" |
| `get_sensor_history` | Returns fuel, temperature, and tire readings over a period. | `assetId`, `from`, `to` (all required) | "How did the fuel level of Volvo FH 4521 change this week?" |

## Fleet checks

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `list_assets_in_geofence` | Lists the assets whose devices are inside a geo object's area. | `geoObjectId` (required), `groupIds` (optional) | "Which vehicles are in the Main depot?" |
| `list_silent_assets` | Lists the assets with a device that is offline or has never reported. | `silentBefore`, `groupIds` (both optional) | "Which vehicles haven't reported since yesterday?" |
| `list_inactive_assets` | Lists the assets that haven't moved since a given time. | `before` (required), `groupIds` (optional) | "Which vehicles haven't moved for a week?" |

## Data queries

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `execute_query` | Runs a read-only [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/) query over the workspace's business data and tracking data. | `query` (required), `variables` (optional) | "List all trailers and their asset groups." |
| `search_schema` | Searches the GraphQL schema for the types and fields that `execute_query` can read. | `query` (required) | "Which fields does an asset have in the API?" |

## Changes

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `asset_create` | Creates an asset of an asset type, with a title and the values of its custom fields. | `typeId`, `title` (required), `customFields` (optional) | "Create a Vehicle asset named Volvo FH 4530: a truck." |
| `asset_type_create` | Creates an asset type with its custom fields. | `title` (required), `fields` (optional) | "Create an asset type named Refrigerated trailer with a Text field Serial number." |
| `geo_object_type_create` | Creates a geo object type with its custom fields. A geo object type can't have a Device field. | `title` (required), `fields` (optional) | "Create a geo object type named Customer site with a Text field Contact phone." |
| `asset_update` | Renames an asset. | `id`, `version`, `title` (all required) | "Rename Volvo FH 4521 to Volvo FH 4521 reserve." |
| `geo_object_create` | Creates a geo object from a GeoJSON geometry. | `typeId`, `title`, `geometry` (all required) | "Create a Depot geo object named North yard around 52.52, 13.40." |
| `geo_object_update` | Changes a geo object's title or geometry. | `id`, `version` (required), `title`, `geometry` (optional) | "Rename the geo object North yard to North depot." |
| `asset_group_create` | Creates an empty asset group. | `title` (required), `typeId`, `color` (optional) | "Create a blue asset group named Winter fleet." |
| `asset_group_update` | Changes an asset group's title or color. | `id`, `version` (required), `title`, `color` (optional) | "Change the Winter fleet group's color to orange." |

## Deletions

Every deletion waits for a person's approval in Navixy. See [How changes work](capabilities.md#how-changes-work).

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `asset_delete` | Deletes an asset. | `id`, `version` (both required) | "Delete the asset Old trailer 3." |
| `geo_object_delete` | Deletes a geo object. | `id`, `version` (both required) | "Delete the geo object Temporary site." |
| `asset_group_delete` | Deletes an asset group. The group's assets aren't deleted. | `id`, `version` (both required) | "Delete the Winter fleet group." |

## Device activation

Every activation waits for a person's approval in Navixy. See [How changes work](capabilities.md#how-changes-work).

| Tool | What it does | Parameters | Example prompt |
| ---- | ------------ | ---------- | -------------- |
| `device_activate` | Activates a GPS device and sends its settings to the SIM card by SMS, for the models that support it. | `modelId`, `imei`, `phone`, `apnName` (required), `title`, `apnUser`, `apnPassword` (optional) | "Activate an FMB920 with IMEI 356307042441013, SIM 491701234567, APN internet." |
| `device_activate_manual` | Activates a GPS device without sending anything to it. You enter the server address on the device yourself. | `modelId`, `imei` (required), `title`, `phone` (optional) | "Activate an FMB920 with IMEI 356307042441013. I set it up myself." |

## See also

* [Navixy MCP Server capabilities](capabilities.md): Learn what the AI client can do in Navixy, with example prompts.
* [Connect Navixy MCP Server](connect-navixy-mcp-server.md): Create an API key and add the server to your AI client.
* [Troubleshooting](troubleshooting.md): Fix an AI client that can't connect or can't make a change.
