---
description: >-
  Make your first Navixy GraphQL API calls: exchange an API key for an access
  token, read your workspace, and activate a GPS device.
tags:
  - guide
  - beta
---

# Getting started

This guide takes you from an API key to a GPS device that is activated and connected to the Navixy platform. You exchange the key for an access token and send your first request with it. Then you find a supported device model, activate the device with its IMEI and SIM data, and check the connection. Every step shows the full request and the response that you get back.

The guide is for developers who are comfortable with HTTP and JSON. It doesn't assume GraphQL experience: every operation is shown in full. See [GraphQL basics](graphql-basics/) to learn the concepts behind the examples.

## Prerequisites

Before you start, make sure that you have the following:

* An API key. Your organization's administrator creates it in Navixy Console and gives it a role. For this guide, the role must allow `read`, `create`, and `update` on devices (`bdr.device`) and `read` on catalog items (`bdr.catalog`). Ask the administrator for the key and for the example request that Console shows next to it. The example request has the ID of your workspace, which the first step needs. See [Get an API key](authentication/README.md#get-an-api-key).
* A GPS device of a model supported by the Navixy platform, its IMEI, and a SIM card with a known phone number. At the moment, seven Teltonika models are supported: FMB920, FMC130, FMB140, FMB125, FMB003, FMC650, and FTC305. The third step shows how to list them.
* The APN settings of your SIM carrier: the APN name and, if the carrier requires them, the user name and the password.
* A terminal with `curl` and [`jq`](https://jqlang.org/). The examples use `jq` to read the access token from a JSON response.

## How activation works

Activation is one mutation, `deviceActivate`. It creates the device record from the IMEI, stores the SIM phone number and the APN settings, and queues an SMS to the SIM card. The SMS contains the APN settings and the address of the platform, so the device can connect and send its data there. Every activated device has the built-in `gps_tracker` device type, so you don't choose a type.

A device has one of four statuses:

<table><thead><tr><th width="230">Status code</th><th>Meaning</th></tr></thead><tbody><tr><td><code>not_activated</code></td><td>The device record exists, but nothing was sent to the device. A device created with <code>deviceCreate</code> starts here and keeps this status: the platform accepts its messages, but its status doesn't change.</td></tr><tr><td><code>awaiting_connection</code></td><td><code>deviceActivate</code> ran, and the SMS is queued. The device hasn't sent anything yet.</td></tr><tr><td><code>active</code></td><td>The first message from the device reached the platform after <code>deviceActivate</code>.</td></tr><tr><td><code>inactive</code></td><td>The platform refuses messages from the device, and <code>deviceActivate</code> refuses its IMEI. No operation of this API sets this status.</td></tr></tbody></table>

Until the device connects, you can run `deviceActivate` again with the same IMEI. The second run replaces the phone number, the APN settings, and the model. It also queues the SMS again. Use a second run to resend a lost SMS or to correct a typo in the phone number or the APN settings. Once the device is `active` or `inactive`, a second activation with its IMEI fails with `DUPLICATE`.

## How to get a token and activate a GPS device

To get an access token, send your first request, and activate a GPS device, follow these steps:

{% stepper %}
{% step %}
### Exchange the API key for an access token

The API doesn't accept the API key itself. Your app exchanges the key for an access token at the Navixy identity service and sends the token with every request. First, put the key in an environment variable, so that it doesn't appear in your commands or in the shell history:

```bash
read -rs NAVIXY_API_KEY && export NAVIXY_API_KEY
```

Paste the key and press Enter. The key doesn't appear on the screen.

Then exchange the key and save the access token in the `ACCESS_TOKEN` variable:

```bash
ACCESS_TOKEN=$(curl -L -s \
  --request POST \
  --url 'https://iam.eu.navixy.com/realms/quickstart/protocol/openid-connect/token' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:token-exchange' \
  --data-urlencode 'client_id=api-key-exchange' \
  --data-urlencode "subject_token=$NAVIXY_API_KEY" \
  --data-urlencode 'subject_token_type=urn:navixy:token-type:api-key' \
  --data-urlencode 'navixy_agent_name=getting-started' \
  --data-urlencode 'navixy_workspace_id=<WORKSPACE_ID>' \
  | jq -r .access_token)
echo "$ACCESS_TOKEN"
```

The command prints the access token: a long string of three parts separated by dots. The exchange returns the token in this response, and `jq` reads the `access_token` field from it:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhY3Rvcl9pZCI6IjAxOWE2YjJmLTc5M2UtODA3Yi04MDAxLTQxNTA0OTRiMDAwMSJ9.c2lnbmF0dXJl",
  "expires_in": 300,
  "token_type": "Bearer",
  "issued_token_type": "urn:ietf:params:oauth:token-type:access_token"
}
```

**Parameters:**

* `subject_token` is the key. The command reads it from `NAVIXY_API_KEY`.
* `navixy_agent_name` is a name for your app, 1 to 100 characters. The token includes it, so you can tell the tokens of your apps apart.
* `navixy_workspace_id` is the ID of your workspace from the example request in Navixy Console. If the key's role covers only one workspace, you can leave this line out.

The token expires after the number of seconds in `expires_in`. If a later step gets an `UNAUTHORIZED` error, run this command again to get a new token.

If the command prints `null`, the exchange failed. Run the `curl` command without `| jq -r .access_token` to see the error. A `workspace_selection_required` error lists the workspaces that the key can reach: repeat the command with one of their IDs in `navixy_workspace_id`. See [Exchange errors](authentication/README.md#exchange-errors) for every error of the exchange.
{% endstep %}

{% step %}
### Send your first request

Navixy GraphQL API has one main endpoint: `https://nvx4.eu.navixy.com/api/graphql`. Send every operation to this endpoint as an HTTP POST request with a JSON body. The body has a `query` field and, optionally, a `variables` field. The following request reads your workspace and confirms that the token works:

```bash
curl -L \
  --request POST \
  --url 'https://nvx4.eu.navixy.com/api/graphql' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query MyWorkspace { bdr { workspace { id title isActive } } }"
  }'
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "id": "019a6b2f-793e-807b-8001-575350430001",
        "title": "FleetOps Ltd",
        "isActive": true
      }
    }
  }
}
```

**Parameters:**

* The `Authorization` header contains the word `Bearer`, a space, and the access token from the previous step. The API uses the token to identify the key and your workspace.
* The `query` field contains the text of your GraphQL query. `MyWorkspace` is a name that you give the query, and it's optional. `workspace { id title isActive }` asks for three fields of your workspace.

Every Business Data Repository operation is inside the `bdr` field, and the response has the same structure under `data.bdr`. The next steps show only the text of each query or mutation and its variables. Send the text in the `query` field and the variables in the `variables` field of the same POST request, with the same `Authorization` header.

If the response has an `errors` array instead of `data`, the token or the request is wrong. An `UNAUTHORIZED` error means that the request had no token or that the token expired: run the previous step again. A `PERMISSION_DENIED` error means that the API couldn't choose the workspace of the request or that the key's role doesn't allow the request. See [API errors](authentication/README.md#api-errors) for every authentication error.
{% endstep %}

{% step %}
### Find a supported device model

`deviceActivate` accepts only a supported model: a model whose data the platform can decode and whose GPS devices it can configure by SMS. List the supported models with the `supported` filter:

```graphql
query SupportedModels {
  bdr {
    deviceModels(filter: { supported: true }, first: 20) {
      nodes {
        id
        code
        title
        supported
        vendor {
          title
        }
      }
    }
  }
}
```

Response, shortened to one model:

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
            "supported": true,
            "vendor": { "title": "Teltonika" }
          }
        ]
      }
    }
  }
}
```

**Parameters:**

* `filter.supported: true` returns only supported models. Without the filter, the list contains every model of the read-only hardware catalog, and `supported` is `false` on most models.
* `first: 20` asks for the first 20 models. To narrow the list to one model, add `filter.titleContains`, for example, `"FMB003"`.

Save the `id` of your model. You'll need it in the next step.
{% endstep %}

{% step %}
### Activate the device

Send the following mutation with the IMEI, the SIM phone number, and the APN settings:

```graphql
mutation ActivateDevice($input: DeviceActivateInput!) {
  bdr {
    deviceActivate(input: $input) {
      device {
        id
        version
        title
        createdAt
        type {
          code
        }
        model {
          title
        }
        status {
          code
          title
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
          "createdAt": "2026-09-30T09:12:41Z",
          "type": { "code": "gps_tracker" },
          "model": { "title": "FMB003" },
          "status": { "code": "awaiting_connection", "title": "Awaiting Connection" },
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

**Parameters:**

`input`:

* `modelId` is the ID from the previous step. The model must be supported. An unsupported model fails with `VALIDATION_ERROR` on `input.modelId`.
* `identifier` is the device IMEI: exactly 15 digits. It becomes the `IMEI` identifier of the device record. An IMEI is unique across the whole Navixy platform.
* `phone` is the SIM card's phone number: 5 to 20 digits, without `+` or spaces. The SMS goes to this number. The number is unique across the platform, so a second device with the same number fails with `DUPLICATE`. To check a number before you activate, see [Activating a device](business-data-repository/guides/activating-a-device.md#check-that-the-phone-numbers-are-available).
* `title` is optional. When you leave it out or send it blank, the API builds a title from the model and the IMEI, for example, `FMB003 356307042772396`.

`input.apn`:

* `name` is the APN of the carrier: 1 to 40 characters of letters, digits, `.`, `_`, or `-`.
* `user` and `password` are optional. Each is at most 40 characters and can also contain `@`. The password goes into the SMS only. The API doesn't store or return it, so send it again when you run the activation again.

The response confirms that the platform recorded the activation and queued the SMS. It doesn't say whether the SMS arrived. The device is now `awaiting_connection`, and `activation` contains the values that were sent, without the password. Save the device `id`: the next step and every later update will use it.
{% endstep %}

{% step %}
### Check that the device is connected

Power on the device with the SIM card inside and wait for the device to receive the SMS and connect. Then read the device status with the following query:

```graphql
query DeviceStatus {
  bdr {
    device(id: "019a6b2f-793e-807b-8001-444556430001") {
      title
      status {
        code
      }
      activation {
        activatedAt
      }
    }
  }
}
```

Response after the device connects:

```json
{
  "data": {
    "bdr": {
      "device": {
        "title": "Truck 01",
        "status": { "code": "active" },
        "activation": { "activatedAt": "2026-09-30T09:12:41Z" }
      }
    }
  }
}
```

**Parameters:**

* `id` is the device ID from the previous step. For an ID that belongs to another workspace, `device` returns `null`.

The platform changes the status to `active` when it accepts the first message from the device, before any position data arrives. Nothing notifies you of the change, so read the device again until the status is `active`.

If the status stays `awaiting_connection`, the SMS didn't reach the device or the device couldn't connect. Check the SIM card, the phone number, and the APN settings. Then run `deviceActivate` again with the same IMEI and the corrected values. The second run replaces the stored SIM and APN data and queues the SMS again.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You exchanged an API key for an access token and sent authenticated requests to Navixy GraphQL API. You also activated a GPS device with its IMEI and SIM data. The device is connected and sends its data to the platform. You can now link its record to an asset.
{% endhint %}

## Error handling

The activation operations return errors in the shared format. See [Error handling](error-handling.md) to learn the response structure and the general codes.

| Code | When it occurs | Resolution |
| --- | --- | --- |
| `UNAUTHORIZED` | The request has no token, or the token is malformed or expired, or another identity service issued it. | Exchange the key again and repeat the request. See [Exchange the API key for an access token](#exchange-the-api-key-for-an-access-token). |
| `PERMISSION_DENIED` | The API can't choose the workspace of the request, or the key's role doesn't allow creating devices. Activating an existing device also requires the right to update it. | Check `navixy_workspace_id` in the exchange or ask your organization's administrator for a key with a role that creates and updates devices. See [Roles and products](authentication/roles-and-products.md). |
| `VALIDATION_ERROR` | `identifier` isn't 15 digits, `phone` isn't 5 to 20 digits, `apn.name` has characters or a length outside its rules, or the model isn't supported. `field` names the argument, for example, `input.identifier` or `input.apn.name`. | Read `field` and `detail` and correct the value. Choose a model from `deviceModels(filter: { supported: true })`. |
| `NOT_FOUND` | `modelId` names no model. | Send the `id` as `deviceModels` returned it. |
| `DUPLICATE` | The IMEI belongs to a device that is `active` or `inactive`, a deleted device, or a device from another workspace. Or the phone number belongs to another device. `field` is `input.identifier` or `input.phone`. | Check the IMEI and the phone number. A deleted device keeps its IMEI reserved, so the same GPS device can't be activated again with a new record. |

Activation with an IMEI that another device already has returns this error:

```json
{
  "errors": [{
    "message": "A device identifier with this type, value, and namespace already exists",
    "path": ["bdr", "deviceActivate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/duplicate",
      "title": "Duplicate Entry",
      "status": 409,
      "detail": "A device identifier with this type, value, and namespace already exists",
      "instance": "/graphql",
      "code": "DUPLICATE",
      "field": "input.identifier",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-30T09:15:00Z"
    }
  }],
  "data": { "bdr": { "deviceActivate": null } }
}
```

### How to handle a duplicate IMEI or phone number

Read `field`. When `field` is `input.identifier`, another device already has this IMEI. A device in your workspace that is `not_activated` or `awaiting_connection` doesn't cause this error, because the activation attaches to that device. So the IMEI belongs to a device that is `active` or `inactive`, a deleted device, or a device in another workspace. To find it in your workspace, look up the device with `devices(filter: { identifierContains: "<IMEI>" })`. When `field` is `input.phone`, another device already has this phone number: correct the number or delete the other device first.

## Next steps

* [Authentication](authentication/README.md): Renew the access token in your app, work with several workspaces, and handle every authentication error.
* [Roles and products](authentication/roles-and-products.md): Learn what the key's role and your workspace's products allow.
* [Managing device records and identifiers](business-data-repository/guides/managing-device-records-and-identifiers.md): Update and delete the device, manage its identifiers, and link it to other devices.
* [Creating assets and assigning devices](business-data-repository/guides/creating-assets-and-assigning-devices.md): Create the vehicles and equipment that devices track and link a device to an asset.
* [Building a live fleet map](tracking/guides/building-a-live-fleet-map.md): Show where your assets are and keep the map current with a state stream.
* [Devices](business-data-repository/api-reference/devices/#deviceactivate): Learn how `deviceActivate`, the `DeviceActivation` type, and every other device operation work.
* [Optimistic locking](optimistic-locking.md): Use the `version` field to keep concurrent updates from overwriting each other.
* [Pagination](pagination.md) and [Filtering and sorting](filtering-and-sorting/): Page through long lists and narrow them down.
* [Core API reference](core-api-reference/): Find every type and operation of the API, grouped by product and category.
