---
description: >-
  Read IoT Logic data through Navixy GraphQL API: what a flow calculated, the
  flows of your workspace, and the flows that a device is in.
tags:
  - guide
  - beta
---

# Reading flows and device data

This guide shows the queries that read IoT Logic data without changing it. Use them to check what a flow calculated, to list your flows, and to find the flows that a device is in before you delete the device.

The examples use the two refrigerated trucks and the cold chain flow from [Creating and updating flows](creating-and-updating-flows.md). The guide is for developers who are comfortable with GraphQL.

## Prerequisites

You need an access token for the workspace you work in, and a role with the `read` action on IoT Logic flows. Every query on this page is sent as an HTTP POST request with a JSON body, as shown in [Prerequisites](creating-and-updating-flows.md#prerequisites) of Creating and updating flows. The steps below show only the document and its variables.

## Checking what a flow calculated

To see what a flow calculated, read the recent values of its devices with the `flowDebugValues` query. It returns the last 12 values of every attribute of up to 10 devices, as the messages leave the flow. In each message, a value that the flow calculated replaces the value from the device, and the value from the device stays where the flow calculated none.

The following query reads the values of one truck of the cold chain flow:

```graphql
query CheckFlow($flowId: ID!, $deviceIds: [ID!]!) {
  iotLogic {
    flowDebugValues(flowId: $flowId, deviceIds: $deviceIds) {
      deviceId
      allFields
      nonNullFields
    }
  }
}
```

Variables:

```json
{
  "flowId": "01a0d2f0-485e-8000-801f-494c464c54be",
  "deviceIds": ["019dbec6-d380-8000-801f-44455643e216"]
}
```

Response, shortened to three attributes and two messages:

```json
{
  "data": {
    "iotLogic": {
      "flowDebugValues": [
        {
          "deviceId": "019dbec6-d380-8000-801f-44455643e216",
          "allFields": {
            "temperature": [
              { "value": 6.2, "messageTime": "2026-09-30T09:41:12.318Z", "serverTime": "2026-09-30T09:41:13.002Z" },
              { "value": 4.8, "messageTime": "2026-09-30T09:40:12.301Z", "serverTime": "2026-09-30T09:40:12.977Z" }
            ],
            "temperature_f": [
              { "value": 43.16, "messageTime": "2026-09-30T09:41:12.318Z", "serverTime": "2026-09-30T09:41:13.002Z" },
              { "value": 40.64, "messageTime": "2026-09-30T09:40:12.301Z", "serverTime": "2026-09-30T09:40:12.977Z" }
            ],
            "is_too_warm": [
              { "value": true, "messageTime": "2026-09-30T09:41:12.318Z", "serverTime": "2026-09-30T09:41:13.002Z" },
              { "value": false, "messageTime": "2026-09-30T09:40:12.301Z", "serverTime": "2026-09-30T09:40:12.977Z" }
            ]
          },
          "nonNullFields": {
            "temperature": [
              { "value": 6.2, "messageTime": "2026-09-30T09:41:12.318Z", "serverTime": "2026-09-30T09:41:13.002Z" },
              { "value": 4.8, "messageTime": "2026-09-30T09:40:12.301Z", "serverTime": "2026-09-30T09:40:12.977Z" }
            ]
          }
        }
      ]
    }
  }
}
```

The newest message comes first. In it, the truck reported 6.2 °C, so the flow calculated `temperature_f` as 43.16 and marked the cargo as too warm. The previous message, at 4.8 °C, was below the limit.

The two maps answer different questions:

* `allFields` has one entry for each recent message. A message without the attribute has an entry with `value: null`, so the entries of all attributes line up by message. In such an entry, `serverTime` has the same value as `messageTime`, because Navixy doesn't record when that message arrived. Use the map to see what the flow did with each message.
* `nonNullFields` has the recent values of each attribute that aren't `null`, and it can go further back than `allFields`. Use it to see the last real value of an attribute that a device reports rarely. The example shows only `temperature` in it, to keep the response short.

Each attribute has at most 12 entries, newest first. `messageTime` is when the device created the message, and `serverTime` is when Navixy received it. Large integers, times, and byte arrays are returned as strings, and durations in milliseconds.

A few more rules apply to the query:

* `deviceIds` takes 1 to 10 device IDs. An empty list or more than 10 different IDs fails with `VALIDATION_ERROR`.
* A device that isn't in the flow, or that your roles don't let you read, is left out of the result. A device of the flow without values yet has empty maps.
* A disabled flow still returns the values that it recorded while it was enabled.
* For a flow that doesn't exist in your workspace, `flowDebugValues` returns `null`.
* The query returns the current values once. To follow them, send the query again.

## Listing flows

To list the flows of your workspace, use `flows`. The query below returns the enabled flows that process one device, with the most recently changed flow first:

```graphql
query ListFlows($filter: IotLogicFlowFilter) {
  iotLogic {
    flows(
      first: 20
      filter: $filter
      orderBy: { field: UPDATED_AT, direction: DESC }
    ) {
      total {
        count
      }
      nodes {
        id
        title
        isEnabled
        deviceCount
        updatedAt
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

Variables:

```json
{
  "filter": {
    "deviceId": "00000000-0000-8000-801f-444556430003",
    "isEnabled": true
  }
}
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "flows": {
        "total": { "count": 1 },
        "nodes": [
          {
            "id": "01a0d2f0-485e-8000-801f-494c464c54be",
            "title": "Cold chain monitoring",
            "isEnabled": true,
            "deviceCount": 2,
            "updatedAt": "2026-09-24T10:22:56.240Z"
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "VVBEQVRFRF9BVDpERVNDOjIwMjYtMDktMjRUMTA6MjI6NTYuMjQwMjUzWnwwMWEwZDJmMC00ODVlLTgwMDAtODAxZi00OTRjNDY0YzU0YmU"
        }
      }
    }
  }
}
```

The filter conditions are combined with AND:

* `titleContains`: The title contains this text. The match ignores letter case.
* `isEnabled`: Only enabled flows, or only disabled flows.
* `deviceId`: Only flows that have this device in one of their data sources.

Without `orderBy`, flows are sorted by title in ascending order. You can also sort by `CREATED_AT`. For details on pages and cursors, see [Pagination](../../pagination.md).

## Finding the flows of a device

To see which flows the devices of your workspace are in, use `deviceFlowLinks`. It returns one link for each device in each data source, across all your flows. A device that is in no flow isn't listed. The query below returns the links of the two refrigerated trucks:

```graphql
query DeviceFlowLinks($filter: IotLogicDeviceFlowLinkFilter) {
  iotLogic {
    deviceFlowLinks(first: 100, filter: $filter) {
      total {
        count
      }
      nodes {
        deviceId
        device {
          title
        }
        flow {
          id
          title
          isEnabled
        }
        entryNode {
          id
          name
        }
        createdAt
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

Variables:

```json
{
  "filter": {
    "deviceIds": [
      "00000000-0000-8000-801f-444556430003",
      "019dbec6-d380-8000-801f-44455643e216"
    ]
  }
}
```

Response:

```json
{
  "data": {
    "iotLogic": {
      "deviceFlowLinks": {
        "total": { "count": 2 },
        "nodes": [
          {
            "deviceId": "00000000-0000-8000-801f-444556430003",
            "device": { "title": "Reefer truck 17" },
            "flow": {
              "id": "01a0d2f0-485e-8000-801f-494c464c54be",
              "title": "Cold chain monitoring",
              "isEnabled": true
            },
            "entryNode": {
              "id": "01a0d2f0-485d-8000-801f-494c4e44be88",
              "name": "Refrigerated trucks"
            },
            "createdAt": "2026-09-24T10:14:07.118Z"
          },
          {
            "deviceId": "019dbec6-d380-8000-801f-44455643e216",
            "device": { "title": "Reefer truck 12" },
            "flow": {
              "id": "01a0d2f0-485e-8000-801f-494c464c54be",
              "title": "Cold chain monitoring",
              "isEnabled": true
            },
            "entryNode": {
              "id": "01a0d2f0-485d-8000-801f-494c4e44be88",
              "name": "Refrigerated trucks"
            },
            "createdAt": "2026-09-24T10:14:07.118Z"
          }
        ],
        "pageInfo": {
          "hasNextPage": false,
          "endCursor": "Q1JFQVRFRF9BVDpERVNDOjIwMjYtMDktMjRUMTA6MTQ6MDcuMTE4WnwwMWEwZDJmMC00ODVlLTgwMDEtODAxZi00OTRjNDQ0YzdhMzE"
        }
      }
    }
  }
}
```

Both trucks are in one flow, at the same data source. A device in two flows, or in two data sources of one flow, has a link for each.

The filter conditions are combined with AND:

* `flowId`: Only the links of this flow.
* `deviceIds`: Only the links of these devices, at most 1,000 IDs. An empty list returns no links. Leave the field out to list the links of every device.

More rules apply to the query:

* `createdAt` is when the device was added to the data source.
* `device` is `null` in rare cases, when the flow kept a device that no longer exists. A device that a flow lists usually can't be deleted, see [Devices in a flow](../how-flows-work.md#devices-in-a-flow). The link stays until you remove the device from the data source, and `deviceId` still names the device.
* Links of devices that your roles don't let you read are left out, also from `total`.
* Without `orderBy`, the newest links come first. You can also sort by `FLOW_TITLE`, the title of the flow.
* A page has at most 100 links. A larger `first` or `last` fails with `VALIDATION_ERROR`, and so does a `deviceIds` list of more than 1,000 IDs.

To list the flows of a single device instead, use `flows` with the `deviceId` filter, as shown in [Listing flows](#listing-flows).

Run one of these queries before you delete a device, because a device that a flow lists can't be deleted. Remove the device from the data source with `flowUpdate`, or delete the flow, then delete the device. See [Devices in a flow](../how-flows-work.md#devices-in-a-flow).

## Errors in this guide

The queries on this page return few errors. These are the common ones:

| Error | Cause | How to fix |
| --- | --- | --- |
| `VALIDATION_ERROR` | A list of IDs is too long: more than 1,000 different devices in the `deviceFlowLinks` filter, or more than 10 different devices in `flowDebugValues`. Or a page asks for more than 100 items | Split the request into smaller ones. |
| `VALIDATION_ERROR` | An ID in the request isn't a valid ID | Correct the ID. |
| `PERMISSION_DENIED` | Your roles don't have the `read` action on IoT Logic flows | Ask an administrator for a role with the action. See [Authentication](../../authentication.md). |

A flow that doesn't exist in your workspace isn't an error: `flow` and `flowDebugValues` return `null`, and devices that you can't read are left out of the results.

## See also

* [Creating and updating flows](creating-and-updating-flows.md): Create the cold chain flow that these examples read, and change it.
* [How IoT Logic flows work](../how-flows-work.md): See how a message moves through a flow and where the calculated attributes go.
* [Flows](../api-reference/flows.md): Look up every field and argument of the queries on this page.
