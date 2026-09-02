---
description: >-
  Make your first Navixy Repository API calls: send an authenticated GraphQL
  request, read your workspace, and register a device.
---

# Getting started

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

This guide takes you from an access token to a working integration with Navixy Repository API. You send your first authenticated request and read your workspace. You then look up the catalog records that a device needs and register a device. Every step shows the full request and the response that you get back.

The guide is for developers who are comfortable with HTTP and JSON. It doesn't assume GraphQL experience: every operation is shown in full, and [GraphQL basics](graphql-basics/README.md) explains the concepts if you need them.

## Prerequisites

Before you start, make sure that you have the following:

* An access token for Navixy Repository API. The token is an OpenID Connect access token from the Navixy identity service. [Authentication](authentication.md) explains the three ways to obtain one.
* The ID of the workspace that you work in. A workspace is the tenant that owns your devices, assets, geo objects, and schedules. Every query and mutation that lists or creates entities takes a `workspaceId` argument. See [Workspaces](workspaces/README.md) for the entity.
* An HTTP client that can send a POST request with a JSON body. The examples use `curl`. The [GraphQL Sandbox](https://api.navixy.dev/v4/graphql/sandbox) works too and needs no setup.

### Find your workspace ID

A token issued for a workspace states it in the `workspace_id` claim, and that value is the ID to use. If your token has no such claim, or you want to confirm the ID, list the workspaces that your token can access:

```graphql
query MyWorkspaces {
  bdr {
    workspaces(first: 10) {
      nodes {
        id
        title
        isActive
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspaces": {
        "nodes": [
          {
            "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
            "title": "FleetOps Ltd",
            "isActive": true
          }
        ]
      }
    }
  }
}
```

The examples on this page use the workspace ID `7c9e6679-7425-40de-944b-e07fc1f90ae7`. Replace it with yours.

## How to send a request

Navixy Repository API has one endpoint. Navixy provides its URL together with your credentials, and `<API endpoint URL>` stands for it in the examples. Send every operation as an HTTP POST request with a JSON body that has a `query` field and, optionally, a `variables` field. Put the access token in the `Authorization` header with the `Bearer` scheme. The following request reads the title of your workspace:

```bash
curl -L \
  --request POST \
  --url '<API endpoint URL>' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query MyWorkspace($id: ID!) { bdr { workspace(id: $id) { id title } } }",
    "variables": { "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7" }
  }'
```

<!-- TODO: replace <API endpoint URL> with the production endpoint at release. The address is deliberately not published while the API is in preview. -->

Response:

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
        "title": "FleetOps Ltd"
      }
    }
  }
}
```

Every Navixy Repository API operation is nested inside the `bdr` field, and the response has the same shape under `data.bdr`. The steps below show only the GraphQL document and its variables. Send each one in the `query` and `variables` fields of the same POST request.

If the response has an `errors` array instead of `data`, the token or the request is wrong. An `UNAUTHORIZED` error means that the token is missing, expired, or invalid. See [Error handling](error-handling.md) for the error format and every error code.

## Steps

The steps register a GPS device. Each step depends on IDs from the previous one, so run them in order.

{% stepper %}
{% step %}

### Read your workspace

Fetch the workspace by ID to confirm that the token and the workspace ID work together. The `version` field is returned for every entity. You send it back with updates to detect concurrent changes.

```graphql
query GetWorkspace($id: ID!) {
  bdr {
    workspace(id: $id) {
      id
      version
      title
      isActive
    }
  }
}
```

Variables:

```json
{
  "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7"
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
        "version": 1,
        "title": "FleetOps Ltd",
        "isActive": true
      }
    }
  }
}
```

A `null` workspace means that no workspace with that ID is visible to your token. Check the ID against the `workspaces` query from the prerequisites.

Workspaces are read-only in Navixy Repository API. They are created, renamed, and closed in Navixy Console.
{% endstep %}

{% step %}

### Look up a device type and a device model

A device needs two catalog records: a device type, which is a classification that you define, and a device model, which comes from the read-only hardware catalog. Run the following query to list the device types of your workspace:

```graphql
query GetDeviceTypes($workspaceId: ID!) {
  bdr {
    deviceTypes(workspaceId: $workspaceId, first: 20) {
      nodes {
        id
        title
        code
      }
    }
  }
}
```

Variables:

```json
{
  "workspaceId": "7c9e6679-7425-40de-944b-e07fc1f90ae7"
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "deviceTypes": {
        "nodes": [
          {
            "id": "b8e3c1f0-1d2a-4e5b-9c8d-111222333444",
            "title": "GPS Tracker",
            "code": "gps_tracker"
          }
        ]
      }
    }
  }
}
```

If `nodes` is empty, create a device type first with the `deviceTypeCreate` mutation. Its input takes the `workspaceId` and a `title`, and the response returns the new `id`.

Then find the hardware model. The following query filters the model catalog by title:

```graphql
query FindDeviceModel($workspaceId: ID!) {
  bdr {
    deviceModels(workspaceId: $workspaceId, filter: { titleContains: "FMB003" }) {
      nodes {
        id
        title
        vendor {
          title
        }
      }
    }
  }
}
```

Variables:

```json
{
  "workspaceId": "7c9e6679-7425-40de-944b-e07fc1f90ae7"
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "deviceModels": {
        "nodes": [
          {
            "id": "c9f4d2e1-2e3b-5f6c-ad9e-222333444555",
            "title": "FMB003",
            "vendor": {
              "title": "Teltonika"
            }
          }
        ]
      }
    }
  }
}
```

**Parameters explained:**

* `workspaceId` limits both lists to one workspace. Device types belong to the workspace that created them. Models are the same in every workspace, but the query still takes the argument.
* `filter.titleContains` matches the model title case-insensitively. Leave the filter out to list every model, or filter by `vendorIds` after looking up the vendor with `deviceVendors`.
* `first: 20` asks for the first 20 device types. Without it, the API returns 20 anyway.

Save the two IDs. The next step needs both.
{% endstep %}

{% step %}

### Create a device

Register the device with its type, its model, and its IMEI:

```graphql
mutation RegisterDevice($input: DeviceCreateInput!) {
  bdr {
    deviceCreate(input: $input) {
      device {
        id
        version
        title
        type {
          title
        }
        model {
          title
        }
        status {
          title
        }
        identifiers {
          id
          type
          value
        }
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "workspaceId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
    "typeId": "b8e3c1f0-1d2a-4e5b-9c8d-111222333444",
    "modelId": "c9f4d2e1-2e3b-5f6c-ad9e-222333444555",
    "title": "Truck 01",
    "identifiers": [
      { "type": "IMEI", "value": "356307042772396" }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "deviceCreate": {
        "device": {
          "id": "e1b6f4a3-4a5d-7b8e-cf10-444555666777",
          "version": 1,
          "title": "Truck 01",
          "type": { "title": "GPS Tracker" },
          "model": { "title": "FMB003" },
          "status": { "title": "Not Activated" },
          "identifiers": [
            {
              "id": "f2c7a5b4-5b6e-8c9f-d011-555666777888",
              "type": "IMEI",
              "value": "356307042772396"
            }
          ]
        }
      }
    }
  }
}
```

**Parameters explained:**

* `workspaceId` is the workspace that owns the device.
* `typeId` and `modelId` are the IDs from the previous step. Both are required.
* `title` is optional. When you leave it out, the API builds one from the vendor, the model, and the identifier, for example `Teltonika FMB003 356307042772396`.
* `identifiers` is optional. Each entry has a `type` from the `DeviceIdType` enum (`IMEI`, `SERIAL_NUMBER`, `MAC_ADDRESS`, and others) and a `value`. An IMEI must be unique across the whole platform, so a second device with the same IMEI fails with a `DUPLICATE` error. You can add identifiers later with `deviceIdentifierAdd`.

Every new device starts in the built-in `Not Activated` status. Save the `id` and the `version` from the response. Every later update and delete of the device takes the `id`, and the `version` protects those calls against concurrent changes.
{% endstep %}

<!-- TODO: add a step for device activation once the activation operation exists. Update, delete, and the rest of the lifecycle stay in guides/working-with-devices.md. -->
{% endstepper %}

{% hint style="success" %}
You sent authenticated requests to Navixy Repository API, read your workspace, and registered a device with a type, a model, and an IMEI. The same request shape, `bdr` block, input object, and `version` field, applies to every other entity in the API.
{% endhint %}

## Next steps

* [Working with devices](guides/working-with-devices.md): Update and delete the device, manage its identifiers, and link it to other devices.
* [Working with assets](guides/working-with-assets.md): Create the vehicles and equipment that devices track, and link a device to an asset.
* [Optimistic locking](optimistic-locking.md): Use the `version` field to keep concurrent updates from overwriting each other.
* [Pagination](pagination.md) and [Filtering and sorting](filtering-and-sorting.md): Page through long lists and narrow them down.
* [Error handling](error-handling.md): The error format, every error code, and how to handle each one.
* [Core API reference](core-api-reference.md): Every type and operation, grouped by entity.
