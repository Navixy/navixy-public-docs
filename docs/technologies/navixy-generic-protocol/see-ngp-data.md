---
description: >-
  See the positions and readings that an NGP 4.0 device sends: in Data Stream
  Analyzer, on the Tracking map, or through Navixy GraphQL API.
tags:
  - guide
---

# See NGP data

In this guide, you see the positions and readings that Navixy saved for a Navixy Generic Protocol (NGP) device. The device page in Location Intelligence shows only the device's details and **Last activity**, so you need another view to check the values. This guide is for developers who test an NGP sender. It describes 3 ways: Data Stream Analyzer, the Tracking map, and Navixy GraphQL API. The guide uses the terms defined in [Key concepts](overview.md#key-concepts), such as packet, attribute name, and NGP device.

## Prerequisites

Before you start, make sure that you have the following:

* An NGP device that sends data, for example, with the script from [Getting started](getting-started.md)
* The device's name, as the **Devices** list in Device management shows it
* Access to IoT Logic in Location Intelligence

To see the data on the Tracking map, you also need an asset that you can assign the device to. To read the data through Navixy GraphQL API, you need an access token. The token gives the access rights of the role that your user or API key has. This role must include the Business Data Repository and Tracking products, and the **View** access right for the device. See [Roles and products](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication/roles-and-products).

## How Data Stream Analyzer works

Data Stream Analyzer is a view in IoT Logic, the Location Intelligence product that processes device data. It shows the latest values of the devices in an IoT Logic flow, and it refreshes every 5 seconds.

A flow is a set of connected nodes. A node is one processing step. To watch a device's data, you need a flow with only 2 nodes: a **Data Source** node with the device and an **Output Endpoint** node. Navixy saves the device's data with or without this flow. You need the flow only to watch the data.

## What Data Stream Analyzer shows for NGP data

Data Stream Analyzer shows the values of NGP packets in the following way:

* Each attribute appears under its attribute name, with the [channel](protocol-specification/message-structure-and-attributes.md#attribute-channels) at the end. For example, `temperature` with channel 2 appears as `temperature_2`. A few names change when Navixy saves them: see [Names that Navixy renames](protocol-specification/message-structure-and-attributes.md#names-that-navixy-renames).
* The position fields appear under IoT Logic names, such as `position_latitude`, `position_speed`, and `position_fix_type`. [Expressions](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/expressions#names-that-are-always-available) lists these names.
* Enum values appear as names, such as `FIX_TYPE_3D`.
* The table shows only values from packets that Navixy saved. See [How Navixy saves packets](protocol-specification/how-navixy-saves-packets.md).
* `event_id`, `buffered`, and `fix_time_ms` don't appear. To check events, use the GraphQL API: see [Through Navixy GraphQL API](#through-navixy-graphql-api).

The table also shows values that arrived before you opened it. Each row is an attribute that Navixy saved for the device at least once. The row shows the value from the newest saved packet, or a dash when the newest packet doesn't have this attribute. [Flow monitoring](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/monitoring) describes the table's controls, such as **Depth** and **Exclude attributes with no value**.

## Example scenario: watch NGP data in Data Stream Analyzer

To build the flow and watch the data, follow these steps:

{% stepper %}
{% step %}
### Create the flow

To create the flow, follow these steps:

1. Go to **IoT Logic**.
2. Click **Create flow**.
3. Enter a **Flow name**, such as `NGP test`.
4. Click **Save**. **Flow enabled** is on by default.

The flow opens on the canvas, the area where you place nodes. The canvas shows a gray example diagram, which isn't part of your flow.
{% endstep %}

{% step %}
### Add the device

To add the device, follow these steps:

1. Drag **Data Source** from the **Start with** group onto the canvas.
2. Double-click the **Data Source** node to open its settings.
3. In **Search devices**, enter the device's name. The search finds devices by name, not by identifier.
4. Select the device.
5. Click **Apply changes**.

The node's **Name** has a default value, such as `Data Source 1`, which you can keep.
{% endstep %}

{% step %}
### Add the output and save

To add the output and save the flow, follow these steps:

1. Drag **Output Endpoint** from the **End with** group onto the canvas. Its mode is already **Default endpoint**.
2. Connect the nodes: drag from the **+** handle of the **Data Source** node to the **Output Endpoint** node.
3. Click **Save flow**.
{% endstep %}

{% step %}
### Watch the data

To watch the data, follow these steps:

1. Above the canvas, in the switch with the options **IoT Logic** and **Data Stream Analyzer**, select **Data Stream Analyzer**. This option is available after you save the flow.
2. On the **Devices** tab, select the device. The table shows values after you select a device.
3. Send a packet with a new value. For example, change `battery_level` in `send_ngp4.py`, then run the script. The new value appears within 5 seconds.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Data Stream Analyzer shows the latest values that Navixy saved for the device.
{% endhint %}

## Other ways to see NGP data

You can also see a device's data in Tracking or through Navixy GraphQL API.

### On the Tracking map

Assign the NGP device to an asset, as [Assign a device to an asset](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/bdr/assets/manage-assets#assign-a-device-to-an-asset) describes. An asset is something that you track, such as a vehicle. Navixy then shows the device's position and readings as the asset's data, and [Tracking](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/tracking) shows the asset on the map.

### Through Navixy GraphQL API

Read the device's current state with the [deviceStates](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/products/tracking/api-reference/state#devicestates) query. The query returns the position, the newest saved packet's time, and the newest value of every attribute. Send it as an HTTP POST request to `https://nvx4.<REGION>.navixy.com/api/graphql`, where `<REGION>` is `eu` or `us`. The request needs an access token: see [Authentication](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication).

The query needs the device's UUID, such as `01a20e36-8953-8000-8001-44455643b575`. Navixy creates the UUID when you activate the device. It isn't the text that you send in `device_id`. The address of the device's page in Device management ends with the UUID.

The following query reads one device's position, readings, and latest events:

```graphql
query DeviceData($deviceIds: [ID!]!) {
  tracking {
    deviceStates(deviceIds: $deviceIds) {
      updatedAt
      position {
        point { lat lng }
        speed
        fixType
      }
      readings {
        code
        channel
        value {
          ... on TrackingBoolValue { boolValue }
          ... on TrackingIntValue { intValue }
          ... on TrackingFloatValue { floatValue }
          ... on TrackingStringValue { stringValue }
        }
      }
      lastEvents {
        vendorCode
        occurredAt
      }
    }
  }
}
```

Variables:

```json
{ "deviceIds": ["01a20e36-8953-8000-8001-44455643b575"] }
```

To send the query with curl, put the query text in the `query` field of the request body and the variables in the `variables` field:

```bash
curl -L \
  --request POST \
  --url 'https://nvx4.<REGION>.navixy.com/api/graphql' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query DeviceData($deviceIds: [ID!]!) { tracking { deviceStates(deviceIds: $deviceIds) { updatedAt position { point { lat lng } speed fixType } readings { code channel value { ... on TrackingBoolValue { boolValue } ... on TrackingIntValue { intValue } ... on TrackingFloatValue { floatValue } ... on TrackingStringValue { stringValue } } } lastEvents { vendorCode occurredAt } } } }",
    "variables": { "deviceIds": ["01a20e36-8953-8000-8001-44455643b575"] }
  }'
```

In the response, each reading has the attribute name in `code` and the channel in `channel`. For example, `temperature_2` returns `code` `temperature` and `channel` 2. `lastEvents` returns the latest events, and `vendorCode` shows an NGP event as `nvx3.<event_id>`. The API returns the fix type without the `FIX_TYPE_` prefix, such as `FIX_3D`.

In this example, the device sent a packet with `event_id` 406 at 09:58. Then the Getting started script sent a packet at 10:00. The response is the following:

```json
{
  "data": {
    "tracking": {
      "deviceStates": [
        {
          "updatedAt": "2026-11-25T10:00:00Z",
          "position": {
            "point": { "lat": 34.159296, "lng": -118.461413 },
            "speed": 43,
            "fixType": "FIX_3D"
          },
          "readings": [
            { "code": "battery_level", "channel": null, "value": { "intValue": 68 } },
            { "code": "board_voltage", "channel": null, "value": { "floatValue": 13.9 } },
            { "code": "ignition", "channel": null, "value": { "boolValue": true } }
          ],
          "lastEvents": [
            { "vendorCode": "nvx3.406", "occurredAt": "2026-11-25T09:58:00Z" }
          ]
        }
      ]
    }
  }
}
```

[Live state](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/products/tracking/api-reference/state) describes every field. [Error handling](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/error-handling) describes the errors that the API returns.

## Error handling

The following table lists the problems that can occur in this guide, with each problem's cause and fix:

| What happens | Cause | What to do |
| --- | --- | --- |
| **Search devices** doesn't find the device | You entered the identifier, but the search finds devices only by name. Or the device is in another workspace: another part of your organization, with its own devices. | Enter the device's name, as the **Devices** list in Device management shows it. Switch to the device's workspace: see [Manage workspaces](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/organization/manage-workspaces). |
| **Data Stream Analyzer** isn't available | The flow isn't saved, or it's turned off. | Click **Save flow**, and check that **Flow enabled** is on. |
| The table shows no values for the device | No device is selected, or Navixy saved no packet for the device. | Select the device on the **Devices** tab. Send a packet with the current time, and check **Last activity**: see [How to check that Navixy saved data](protocol-specification/how-navixy-saves-packets.md#how-to-check-that-navixy-saved-data). |
| A value that you sent doesn't appear | Navixy skipped the attribute or didn't save the packet. Or Navixy saved the attribute under another name. Or Data Stream Analyzer doesn't show this field. | Check the attribute against [Name rules](protocol-specification/message-structure-and-attributes.md#name-rules), and the packet against [Which packets Navixy doesn't save](protocol-specification/how-navixy-saves-packets.md#which-packets-navixy-doesnt-save). Look for the saved name in [Names that Navixy renames](protocol-specification/message-structure-and-attributes.md#names-that-navixy-renames). Check `event_id` [through Navixy GraphQL API](#through-navixy-graphql-api). |

## See also

* [Getting started](getting-started.md): Send a first NGP packet and check that Navixy saved it.
* [How Navixy saves packets](protocol-specification/how-navixy-saves-packets.md): Learn why a packet that you sent doesn't appear.
* [Flow monitoring](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/monitoring): Learn every control of Data Stream Analyzer.
