---
hidden: true
noIndex: true
description: >-
  Find out what your AI client can do in your Navixy workspace, from finding a
  vehicle to deleting a geo object, with example prompts.
tags:
  - reference
  - beta
---

# Navixy MCP Server capabilities

You can ask your AI client to find a vehicle, review its trips, or create a geo object. The client will do it in your Navixy workspace. It uses the so-called tools provided by Navixy MCP Server: operations that the server supports. Your AI client decides which tool to use, so you never need to call a tool by name. When you aren't sure what's possible, ask the client what it can do in Navixy.

This page describes what you can achieve through the AI client. For the list of tools with their parameters, see [Navixy MCP Server tools](tools.md). If the AI client isn't connected to the MCP server yet, see [Connect Navixy MCP Server](connect-navixy-mcp-server.md).

## What you can do

### Find vehicles and check their status

The AI client finds an asset by a part of its title or license plate, so you don't need the exact name. It tells you the vehicle's last known position and whether the vehicle is moving, stopped, or parked. It also tells you when the device last reported and the latest sensor values.

```
Where is Volvo FH 4521 now?
```

```
Which vehicles of the North group are moving right now, and how fast?
```

### Review trips and routes

The AI client lists a vehicle's trips and stops over a period, with distances, driving time, and stop time. It can also follow the route point by point. Enter a period and mention your time zone if it matters: Navixy returns times in UTC.

```
How many kilometers did Volvo FH 4521 drive last week?
```

```
Show me the route that Volvo FH 4521 took yesterday afternoon, with its stops.
```

### Check driving safety and sensor history

The AI client lists the harsh acceleration, harsh braking, harsh turns, and SOS alarms that GPS devices reported. Not every device model reports these events. It also reads how fuel level, temperature, and tire pressure changed over a period. Speeding isn't included. Sensor history comes from trips only, so a parked vehicle has no history for that time.

```
Did Volvo FH 4521 have any harsh braking this month?
```

```
How did the fuel level of Volvo FH 4521 change over the last three days?
```

### Find vehicles that need attention

The AI client finds vehicles whose devices stopped reporting, vehicles that haven't moved since a given time, and vehicles inside a geo object. One question checks up to 1,000 vehicles. For a larger fleet, name an asset group.

```
Which vehicles haven't reported since yesterday morning?
```

```
Which vehicles are inside the Main depot geo object now?
```

### Look up other workspace data

The AI client can also read other workspace data: asset details, asset types, asset groups, devices, geo objects, schedules, tags, and audit log. In the audit log, it can see who made a change and when, but not the values before and after the change. It can't read organizations, Console members and roles, billing, or IoT Logic flows.

```
List all trailers and the asset groups that they belong to.
```

```
Who changed Volvo FH 4521 last, and when?
```

### Make changes

The AI client can create or rename an asset, create or change a geo object, and create or change an asset group. It can also create an asset type or a geo object type with its custom fields. When it creates an asset, it also fills in the asset's custom fields and can assign it a GPS device that isn't already assigned to another asset. It can also delete an asset, a geo object, or an asset group after a person approves the deletion in Navixy. See [How changes work](#how-changes-work).

```
Create a Vehicle asset named Volvo FH 4530: a truck with the license plate B-TR 4530.
```

```
Rename Volvo FH 4521 to Volvo FH 4521 reserve.
```

```
Create an asset type named Refrigerated trailer with a Text field Serial number and a Decimal field Capacity.
```

```
Create a Depot geo object named North yard: a square of 200 meters around 52.52, 13.40.
```

### Activate GPS devices

The AI client can activate a GPS device based on its model and IMEI. Knowing the SIM card's phone number and APN, Navixy can send the settings to the device by SMS. Navixy can't send the SMS for every model, and the AI client tells you when you need to set up the device yourself. Every activation waits for a person's approval.

```
Activate a Teltonika FMB920 with IMEI 356307042441013. The SIM number is 491701234567, and the APN is internet.
```

## How changes work

The AI client makes one change with each request. It makes a change only when the API key's role allows it. Every change is recorded in the audit log under the key's name.

Creating, renaming, and other changes happen at once. These changes wait for a person's approval first:

* Every deletion
* Every device activation
* Any change after the daily limit is reached

When a change needs approval, the AI client gives you a link to the approval page in Navixy. A person whose role allows making this change opens the link, signs in again, and approves or rejects it. After the approval, ask the AI client to continue. The approval and change must both happen within 24 hours of the request. After that, the request expires, and you should ask the AI client again.

The AI client can't do all operations available in the Navixy UI:

* It fills in custom fields only when creating an asset. It can't fill in a **Geo data** field, so it can't create an asset whose type requires this field.
* It can't create a geo object or change an asset or a geo object while a required custom field has no value.
* It can't move a GPS device from one asset to another. A new asset can't get a device that another asset uses.
* It creates custom fields of these types only: Text, Text area, Integer, Decimal, Checkbox, Date, Date and time, Dropdown, and Device. A geo object type can't get a Device field.
* It can't change or delete an asset type or a geo object type.
* It can't add assets to a group or remove them from it.
* It can't delete an asset, a geo object, or an asset group that is still in use, such as an asset that belongs to a group.

## Limits

Navixy sets these limits on its servers and can change them.

| Limit | Value |
| ----- | ----- |
| Requests | 100 per minute for each API key |
| Changes without approval | 50 per day for each API key and 300 per day for each workspace. Approved changes count too. Further changes wait for approval. The day starts at 00:00 UTC. |
| Time to approve and make a change | 24 hours from the request |
| Vehicles in one fleet check | Up to 1,000 checked, up to 100 listed |
| Answer size | A long period of trips or readings comes in parts. The AI client reads the parts one after another. |

## See also

* [Navixy MCP Server tools](tools.md): Find every tool with its parameters.
* [Connect Navixy MCP Server](connect-navixy-mcp-server.md): Create an API key and add the server to your AI client.
* [Troubleshooting](troubleshooting.md): Fix an AI client that can't connect or can't make a change.
* [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Console documentation: Learn how the key's role limits what the AI client can do.
