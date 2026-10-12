---
description: >-
  Activate a batch of GPS devices through Navixy GraphQL API, correct a wrong
  SIM number, resend a lost SMS, and watch which devices are still waiting to
  connect.
tags:
  - guide
  - beta
---

# Activating a device

This guide shows how to activate GPS devices in your workspace through the Business Data Repository API and how to handle what goes wrong on the way: a wrong SIM number, a lost SMS, and a device that never connects. You will activate a batch of devices, correct one of them, resend the settings to another, and list the devices that are still waiting to connect.

Every operation on this page acts in the workspace named by your access token. For the shortest path from a token to one activated device, see [Getting started](../../getting-started.md). This guide starts where that page ends.

## Prerequisites

You need an access token for the workspace, with a role that creates and updates devices. See [Authentication](../../authentication.md) for how to get a token and what your roles allow. For each device you also need its IMEI, the phone number of its SIM card, and the APN settings of the carrier.

Activation needs the ID of a supported device model. List the supported models, narrowed to the model you hold:

```graphql
query SupportedModel {
  bdr {
    deviceModels(filter: { supported: true, titleContains: "FMB003" }) {
      nodes {
        id
        code
        title
        vendor {
          title
        }
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
      "deviceModels": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-43444d4c0005",
            "code": "fmb003",
            "title": "FMB003",
            "vendor": { "title": "Teltonika" }
          }
        ]
      }
    }
  }
}
```

An empty list means that the model isn't supported: the platform doesn't decode its data, so `deviceActivate` refuses it. Only supported models can be activated.

The last step of the scenario filters devices by status. Read the IDs of the built-in statuses once:

```graphql
query DeviceStatuses {
  bdr {
    deviceStatuses {
      nodes {
        id
        code
        title
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
      "deviceStatuses": {
        "nodes": [
          { "id": "019a6b2f-793e-807b-8001-434453540001", "code": "not_activated", "title": "Not Activated" },
          { "id": "019a6b2f-793e-807b-8001-434453540002", "code": "awaiting_connection", "title": "Awaiting Connection" },
          { "id": "019a6b2f-793e-807b-8001-434453540003", "code": "active", "title": "Active" },
          { "id": "019a6b2f-793e-807b-8001-434453540004", "code": "inactive", "title": "Inactive" }
        ]
      }
    }
  }
}
```

Save the ID of `awaiting_connection`. Status IDs are the same in every workspace, so you can read them once.

## How activation works

`deviceActivate` does three things in one call. It creates the device record with the IMEI as its identifier, stores the SIM phone number and the APN settings on the device, and queues an SMS to the SIM card. The SMS carries the APN settings and the address of the Navixy platform. When the device receives it, the device connects to the platform on its own. Every activated device gets the built-in `gps_tracker` type, so the input takes no type.

Three rules decide what happens to a second activation with the same IMEI:

* While the device is `not_activated` or `awaiting_connection`, the call attaches to it. It replaces the phone number, the APN settings, and the model, replaces the title when you send one, and queues the SMS again. Use it to correct a typo or to resend a lost SMS.
* Once the device is `active` or `inactive`, the IMEI is taken, and the call fails with `DUPLICATE` on `input.identifier`.
* An IMEI that belongs to a device of another workspace fails the same way. The error doesn't say which workspace holds it.

The phone number is unique across the platform too. A second device with the same number fails with `DUPLICATE` on `input.phone`. The APN password goes into the SMS only: the API neither stores nor returns it, so every re-run needs it again.

A device that was created with `deviceCreate` can be activated later with the same call. It attaches to the existing record, because that record is `not_activated`.

[Devices](../api-reference/devices/README.md#deviceactivate) documents every field of the input and of `DeviceActivation`.

## Example scenario: A fleet operator activates a shipment of devices

TransLog GmbH runs delivery trucks out of Hamburg. A shipment of three Teltonika FMB003 devices arrived, each with a SIM card from the same carrier. The administrator activates the three devices, then finds that one SIM number was typed wrong and that another device didn't receive its SMS.

{% stepper %}
{% step %}
### Activate the first device

Send the IMEI, the SIM phone number, and the APN settings:

```graphql
mutation ActivateDevice($input: DeviceActivateInput!) {
  bdr {
    deviceActivate(input: $input) {
      device {
        id
        version
        title
        status {
          code
        }
        identifiers {
          type
          value
        }
        activation {
          phone
          apn {
            name
            user
          }
          activatedAt
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
    "modelId": "019a6b2f-793e-807b-8001-43444d4c0005",
    "title": "Truck 01",
    "identifier": "356307042772396",
    "phone": "4915112345678",
    "apn": {
      "name": "internet.example.net",
      "user": "iot",
      "password": "iot"
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "deviceActivate": {
        "device": {
          "id": "019a6b2f-793e-807b-8001-444556430001",
          "version": 2,
          "title": "Truck 01",
          "status": { "code": "awaiting_connection" },
          "identifiers": [
            { "type": "IMEI", "value": "356307042772396" }
          ],
          "activation": {
            "phone": "4915112345678",
            "apn": { "name": "internet.example.net", "user": "iot" },
            "activatedAt": "2026-09-30T09:12:41Z"
          }
        }
      }
    }
  }
}
```

The device exists, holds its IMEI, and is `awaiting_connection`. The response says that the platform queued the SMS, not that the SMS arrived. `activation` shows what was sent, without the password.

Key fields:

* `identifier` must be exactly 15 digits. It becomes the `IMEI` identifier of the device.
* `phone` is 8 to 20 digits, with no `+`, spaces, or dashes.
* `apn.name` is 1 to 40 characters of letters, digits, `.`, `_`, or `-`. `apn.user` and `apn.password` are optional and at most 40 characters each, with `@` allowed too.
* `title` is optional. Without it, the API names the device after the vendor, the model, and the IMEI.
{% endstep %}

{% step %}
### Activate the other devices in one request

A `bdr` block runs one mutation at a time, so give each device its own aliased `bdr` block (see [Aliases](../../graphql-basics/graphql-tips-and-patterns.md#aliases)). The root fields run in the order you write them:

```graphql
mutation ActivateShipment {
  truck02: bdr {
    deviceActivate(input: {
      modelId: "019a6b2f-793e-807b-8001-43444d4c0005"
      title: "Truck 02"
      identifier: "356307042772404"
      phone: "4915112345679"
      apn: { name: "internet.example.net", user: "iot", password: "iot" }
    }) {
      device { id title status { code } }
    }
  }
  truck03: bdr {
    deviceActivate(input: {
      modelId: "019a6b2f-793e-807b-8001-43444d4c0005"
      title: "Truck 03"
      identifier: "356307042772412"
      phone: "4915112345670"
      apn: { name: "internet.example.net", user: "iot", password: "iot" }
    }) {
      device { id title status { code } }
    }
  }
}
```

Response:

```json
{
  "data": {
    "truck02": {
      "deviceActivate": {
        "device": { "id": "019a6b2f-793e-807b-8001-444556430002", "title": "Truck 02", "status": { "code": "awaiting_connection" } }
      }
    },
    "truck03": {
      "deviceActivate": {
        "device": { "id": "019a6b2f-793e-807b-8001-444556430003", "title": "Truck 03", "status": { "code": "awaiting_connection" } }
      }
    }
  }
}
```

When one activation fails, the others still run. The failed alias holds `null`, and its error names the alias in `path`, for example `["truck03", "deviceActivate"]`. Read `errors` for every alias before you treat the batch as done.
{% endstep %}

{% step %}
### Correct a wrong SIM number

Truck 02 doesn't connect. The administrator finds that its phone number ends in 79 on the SIM card, not 78 as typed. Run the activation again with the same IMEI and the corrected number. The device is still `awaiting_connection`, so the call attaches to it:

```graphql
mutation CorrectPhone {
  bdr {
    deviceActivate(input: {
      modelId: "019a6b2f-793e-807b-8001-43444d4c0005"
      identifier: "356307042772404"
      phone: "4915112345679"
      apn: { name: "internet.example.net", user: "iot", password: "iot" }
    }) {
      device {
        id
        version
        title
        status { code }
        activation {
          phone
          activatedAt
        }
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
      "deviceActivate": {
        "device": {
          "id": "019a6b2f-793e-807b-8001-444556430002",
          "version": 3,
          "title": "Truck 02",
          "status": { "code": "awaiting_connection" },
          "activation": {
            "phone": "4915112345679",
            "activatedAt": "2026-09-30T10:03:15Z"
          }
        }
      }
    }
  }
}
```

The same device came back: the `id` didn't change, `version` moved on, and `title` stayed because the input didn't send one. `activation.phone` holds the corrected number, `activatedAt` is the time of the re-run, and the SMS was queued again. Send the APN password again on every re-run, because the API doesn't keep it.

A wrong IMEI can't be corrected this way. A re-run with another IMEI and the same phone number fails with `DUPLICATE` on `input.phone`, because the phone number still belongs to the first device, and nothing is created. Delete the device with the wrong IMEI first, then activate again.
{% endstep %}

{% step %}
### Resend a lost SMS

Truck 03 has the right number but didn't receive the SMS. Run the activation again with the same input. Nothing changes on the device except `activatedAt` and `version`, and the SMS is queued once more:

```graphql
mutation ResendSettings {
  bdr {
    deviceActivate(input: {
      modelId: "019a6b2f-793e-807b-8001-43444d4c0005"
      identifier: "356307042772412"
      phone: "4915112345670"
      apn: { name: "internet.example.net", user: "iot", password: "iot" }
    }) {
      device {
        id
        status { code }
        activation { activatedAt }
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
      "deviceActivate": {
        "device": {
          "id": "019a6b2f-793e-807b-8001-444556430003",
          "status": { "code": "awaiting_connection" },
          "activation": { "activatedAt": "2026-09-30T10:05:02Z" }
        }
      }
    }
  }
}
```

The response confirms that the SMS was queued, not that it left the platform. An SMS that the platform couldn't send is logged on the server and not reported to you.
{% endstep %}

{% step %}
### List the devices that are still waiting to connect

The platform moves a device to `active` when it admits the first message from it. Nothing notifies you, so list the devices that are still `awaiting_connection`, with the status ID from the prerequisites:

```graphql
query WaitingToConnect {
  bdr {
    devices(
      filter: { statusIds: ["019a6b2f-793e-807b-8001-434453540002"] }
      first: 50
    ) {
      nodes {
        id
        title
        activation {
          phone
          activatedAt
        }
      }
      total {
        count
      }
    }
  }
}
```

Response, an hour after the re-runs:

```json
{
  "data": {
    "bdr": {
      "devices": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-444556430003",
            "title": "Truck 03",
            "activation": { "phone": "4915112345670", "activatedAt": "2026-09-30T10:05:02Z" }
          }
        ],
        "total": { "count": 1 }
      }
    }
  }
}
```

Truck 01 and Truck 02 connected and left the list. Truck 03 is still waiting after the resend, so the problem is on the device side: the SIM card, its data plan, or the power. The list is ordered by title. It's the same `devices` query as everywhere else, so combine `statusIds` with `identifierContains` or `titleContains` to find one device.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Three GPS devices were activated in your workspace, one SIM number was corrected, one SMS was resent, and the list of devices still waiting to connect tells you where to look next. Every call acted in the workspace named by your token.
{% endhint %}

## Related operations

**Read the activation of one device.** `device(id:)` returns `activation` with the phone number, the APN name and user, the activation code, and `activatedAt`. The field is `null` for a device created with `deviceCreate` that was never activated. For a device of another workspace, `device` itself returns `null`.

**Rename a device.** Once the device is active, change its title with `deviceUpdate`. Send the `version` from your last read, so a concurrent change isn't overwritten. See [Managing device records and identifiers](managing-device-records-and-identifiers.md#update-a-device).

**Find every activated device.** Filter `devices` by the status of your choice. The four built-in statuses are `not_activated`, `awaiting_connection`, `active`, and `inactive`. Only `deviceActivate` sets `awaiting_connection`, and only the platform sets `active`.

**Retire a device.** `deviceDelete` hides the device and detaches it from the assets that used it. A required `DEVICE` field that holds the device, or an IoT Logic flow that lists it in a workspace that has IoT Logic, refuses the delete with `IN_USE`; see [Deleting a device](managing-device-records-and-identifiers.md#deleting-a-device). The IMEI stays reserved by the deleted record, so the same hardware can't be activated again with a new record today. The phone number is released.

## Error handling

The activation operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes.

| Code                | When it occurs                                                                                                                                                                                                | Resolution                                                                                                                                             |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `PERMISSION_DENIED` | The API couldn't tell which workspace the request acts in, or your roles don't allow creating devices. Attaching to an existing device also needs the right to update it.                                    | Request a token issued for the workspace, or ask an administrator of your organization for a role that creates and updates devices.                    |
| `VALIDATION_ERROR`  | `identifier` isn't 15 digits, `phone` isn't 8 to 20 digits, `activationCode` isn't 3 to 20 digits, `apn.name` breaks its format, or the model isn't supported. `field` names the argument.                    | Read `field` and `detail` and correct the value. Pick the model from `deviceModels(filter: { supported: true })`.                                      |
| `NOT_FOUND`         | `modelId` names no model.                                                                                                                                                                                     | Send the `id` as `deviceModels` returned it.                                                                                                           |
| `DUPLICATE`         | The IMEI belongs to a device that is `active` or `inactive`, to a deleted device, or to a device of another workspace (`input.identifier`). Or the phone number belongs to another device (`input.phone`).   | Check the IMEI and the phone number against the SIM card and the device label. For a wrong IMEI on an existing record, delete that record first.       |

Activating with a model that the platform doesn't support returns this error:

```json
{
  "errors": [{
    "message": "Device model 'fmb900' is not supported by the platform",
    "path": ["bdr", "deviceActivate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/validation",
      "title": "Validation Error",
      "status": 400,
      "detail": "Device model 'fmb900' is not supported by the platform",
      "instance": "/graphql",
      "code": "VALIDATION_ERROR",
      "field": "input.modelId",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-30T10:45:00Z"
    }
  }],
  "data": { "bdr": { "deviceActivate": null } }
}
```

### How to handle an unsupported model

Read `field`: `input.modelId` means that the model exists but isn't supported. List the supported models with `deviceModels(filter: { supported: true })` and pick the one that matches the hardware. If your hardware isn't in that list, the platform can't activate it by SMS. Register it with `deviceCreate` instead and configure it by other means.

Activating a second device with a phone number that is already in use returns this error:

```json
{
  "errors": [{
    "message": "A device with this phone number is already registered",
    "path": ["bdr", "deviceActivate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/duplicate",
      "title": "Duplicate Entry",
      "status": 409,
      "detail": "A device with this phone number is already registered",
      "instance": "/graphql",
      "code": "DUPLICATE",
      "field": "input.phone",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-30T10:45:00Z"
    }
  }],
  "data": { "bdr": { "deviceActivate": null } }
}
```

### How to handle a duplicate phone number

Find the device that holds the number: it's the one you activated with it, so search by the IMEI you used then with `devices(filter: { identifierContains: "<IMEI>" })`. If that record has the wrong IMEI, delete it and activate again. If the number is really shared, the SIM card is in the wrong device.

## See also

* [Getting started](../../getting-started.md): The shortest path from a token to one activated device.
* [Managing device records and identifiers](managing-device-records-and-identifiers.md): Update and delete devices, and manage their identifiers.
* [Creating assets and assigning devices](creating-assets-and-assigning-devices.md): Link the activated devices to the vehicles they track.
* [Devices](../api-reference/devices/README.md): Every device operation and type, including `deviceActivate` and `DeviceActivation`.
* [Error handling](../../error-handling.md): The error format, every error code, and how to handle each one.
