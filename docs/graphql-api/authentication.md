---
description: >-
  How Navixy GraphQL API authenticates requests: how to get and send an access
  token, how the API checks it and picks your workspace, what your roles allow,
  and the errors that authentication returns.
tags:
  - fundamentals
  - beta
---

# Authentication

Navixy GraphQL API authenticates every request with an access token. The token is a signed [JSON Web Token](https://www.rfc-editor.org/rfc/rfc7519.html) issued by the Navixy identity service. The API identifies you from the token, and uses the token to pick your workspace and check your roles. The API never sees your password.

{% hint style="info" %}
Navixy GraphQL API is in early access. Sign-in details for the API are not self-service yet.

To become one of the first to try it, <a href="https://navixy.com/en/contact?utm_source=navixy-docs&#x26;utm_campaign=api-early-access" class="button primary">Contact Navixy</a>
{% endhint %}

This page is for developers who call Navixy GraphQL API. It covers how to get and send a token, how the API checks the token, how the API picks your workspace, what your roles allow, and the errors that authentication returns.

## How to get an access token

Access to Navixy GraphQL API is by invitation while the API is in early access. When Navixy onboards you, you receive these details:

* The sign-in details for the Navixy identity service
* The workspace that you work in
* The token lifetime and how to renew a token before it expires

The sign-in method doesn't change anything on this page. Once you have a valid token, every request works the same way. A token expires at the end of its lifetime, so renew it before then.

### Tokens for scripts and backend services

Navixy GraphQL API doesn't accept API keys, and the identity service issues tokens only to people. A script or a backend service therefore sends a token issued to a person. Every request from the script has the roles of that person in the workspace. When that person loses access, the script loses access too. Your onboarding details say how to renew the token.

## How to send an access token

Send the token in the `Authorization` header of every request, with the `Bearer` scheme. The following request reads the ID and the title of your workspace:

```bash
curl -L \
  --request POST \
  --url 'https://nvx4.eu.navixy.com/api/graphql' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query MyWorkspace { bdr { workspace { id title } } }"
  }'
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "id": "019a6b2f-793e-807b-8001-575350430001",
        "title": "FleetOps Ltd"
      }
    }
  }
}
```

A request without the `Authorization` header doesn't return HTTP 401. The API returns HTTP 200 with an `UNAUTHORIZED` error, with `detail` `Authentication required`, on every field that needs a token, such as `bdr` and `node`. Nothing under those fields runs. Introspection needs a token too: without one, `__schema` and `__type` return an `UNAUTHORIZED` error with `detail` `Introspection requires authentication`, and no part of the schema. If you get this error on every request, check the header first.

## How the API checks a request

The API checks every request in four steps:

1. It checks that the token is valid. It checks the signature, the issuer (`iss`), and the validity period (`exp` and `nbf`). If the API requires an audience, the `aud` claim must name it. A token that Navixy issues for you passes these checks. An invalid token returns `UNAUTHORIZED` with HTTP 401.
2. It identifies you by the `sub` claim. On your first request, the API creates a user record for you from the `sub`, `email`, `given_name`, and `family_name` claims. Nothing else in the token decides who you are.
3. It picks the workspace that the request acts in. See [How the API picks your workspace](authentication.md#how-the-api-picks-your-workspace).
4. It checks each field of the request against your roles and the products of your workspace. See [What your roles and products allow](authentication.md#what-your-roles-and-products-allow).

The API reads only the claims listed in [Access token claims](authentication.md#access-token-claims). It ignores every other claim in the token.

## How the API picks your workspace

A workspace owns your devices, assets, geo objects, and schedules. Every operation of Business Data Repository and Tracking acts in one workspace, and the API picks that workspace for the whole request before any field runs. For this reason, no operation takes a workspace ID.

The API picks the workspace in one of two ways:

* If the token has a `workspace_id` claim, the API uses that workspace.
* If the token has no `workspace_id` claim, the API uses the workspace of your active membership. This works only when you have exactly one active membership.

The API refuses the request in these cases:

* The `workspace_id` claim isn't a workspace ID, or it's a workspace ID from another Navixy region or environment.
* Navixy Console doesn't confirm the workspace, or the API can't reach Console to check it.
* The workspace was closed, or Navixy Console suspended it.
* The token has no `workspace_id` claim, and you have no active membership or more than one.

A refusal is a `PERMISSION_DENIED` error. Its `detail` names the reason, for example `the caller belongs to more than one workspace and the token names none`. Every field of Business Data Repository and Tracking except `me` needs the workspace. The field that you select returns this error, and because the entry points can't be `null`, `data` is `null` for the operation.

To see the workspace that you act in, query it:

```graphql
query MyWorkspace {
  bdr {
    workspace {
      id
      title
      isActive
    }
  }
}
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

The `workspace` field is non-null and takes no argument.

Entities belong to the workspace too. A by-id query such as `device(id:)` returns `null` for an ID from another workspace, and a list query returns only the entities of your workspace.

### Switching to another workspace

A token acts in one workspace. To act in another workspace, you need a token issued for that workspace. Your onboarding details say how to get one.

To list the organizations and workspaces that you can reach, query `me` under `bdr`. See [Users](business-data-repository/api-reference/actors/users.md). The `console` entry point lists them as Navixy Console reports them. See [Console API reference](console/api-reference/).

Workspaces are read-only in Navixy GraphQL API. You create, rename, and close workspaces in Navixy Console. See [Workspaces](business-data-repository/api-reference/workspaces/) for the entity.

### Billing my\* operations

The Billing [my\* operations](billing/#purpose-and-capabilities), such as `mySubscribe`, act in the workspace too. They read only the `workspace_id` claim. Without the claim, they refuse the request and don't use your membership.

These operations check that the claim is a workspace ID of this Navixy region and environment. They don't check that the workspace exists. For a workspace that doesn't exist, `myAvailablePlans` returns the plans open to every workspace, and `mySubscribe` fails with `VALIDATION_ERROR`.

## What your roles and products allow

Your roles decide what you can read and change in your workspace. An administrator of your organization assigns your roles in Navixy Console or through the [IAM operations](iam/). A member who was just added to the organization has no roles until someone assigns one. Without a Business Data Repository role, the only field you can read in `bdr` is `me`.

A role applies only in the workspaces that it covers. The token lists the roles that you hold in the token's workspace, so a role that you get or lose takes effect when you get a new token. A change to the role itself, such as a new action, takes effect within about 2 minutes, without a new token.

`me { roles }` lists the roles that you hold. With a role that covers IAM, the [IAM role operations](iam/api-reference/roles.md) show what each role grants. A request that your roles don't allow returns an error.

A role grants actions on resources. Each resource has a code, which is what the [IAM role operations](iam/api-reference/role-access.md) use. Business Data Repository has the actions `read`, `create`, `update`, and `delete` on these resources:

| Resource | Code |
| --- | --- |
| Devices | `bdr.device` |
| Assets | `bdr.asset` |
| Asset groups | `bdr.asset-group` |
| Geo objects | `bdr.geo-object` |
| Schedules | `bdr.schedule` |
| Inventories | `bdr.inventory` |
| Members | `bdr.member` |
| Integrations | `bdr.integration` |
| Catalog items that belong to your workspace | `bdr.catalog` |

The audit log, `bdr.audit`, has the `read` action only. It covers the events of every entity in the workspace, whatever your access to the entities themselves.

Each kind of operation needs a matching action. A query needs `read`, and a create needs `create`. An update needs `update` and `read` on the same entity, and a delete needs `delete` and `read`. To find the IDs of the types and models that a new device or asset needs, a role also needs `read` on `bdr.catalog`, because types and models are catalog items.

IoT Logic has the actions `read`, `create`, `update`, and `delete` on flows, with the code `iot_logic.flow`. A role with one of these actions has it on every flow of the workspace. The devices in a flow follow your access to devices in Business Data Repository: a flow shows and changes only the devices that your roles let you read. See [Who does what](iot-logic/#who-does-what) in IoT Logic.

Tracking and Billing have no resources of their own. Tracking returns data only for the assets and devices that your roles let you read in Business Data Repository. Billing checks that your workspace has the product and that one of your roles covers Billing.

A role can grant an action on every entity of a resource or only on some of the entities. When a role covers only some entities of a resource, the API hides the others:

* A list or a connection returns only the entities that your roles let you read, and its `total` counts only them.
* A lookup by ID, such as `device(id:)`, and `node(id:)` return `null`.
* A list inside another entity, such as the devices of an inventory or the assets of an asset group, leaves out the entities you can't read. A field inside another entity that can be `null` returns `null`.
* A mutation on an entity that your roles don't cover returns `PERMISSION_DENIED`.

When your roles don't allow a mutation, or a field that can't be `null`, the API returns a `PERMISSION_DENIED` error with `detail` `Access Denied`. The error doesn't name the missing action. A field that your roles don't let you read at all, such as `devices` without any `read` on `bdr.device`, returns this error too. Three fields inside other entities can't be `null` and return this error for an entity you can't read: the `device` of a device custom field value, the `device` of an inventory assignment, and the `asset` of an asset group membership.

{% hint style="warning" %}
A refusal on a root list, a connection, or `workspace` makes the whole `data` object `null`, because these fields can't be `null`. The request loses every other field with it. When you aren't sure that your roles allow such a field, send it in a separate request. See [Partial failure](error-handling.md#partial-failure).
{% endhint %}

Your workspace must also have the product. The token lists the products of your workspace: the products that its plan covers and that are turned on in it. When the list doesn't name Business Data Repository (`bdr`), the API refuses every Business Data Repository field and mutation that needs a role, with the `PERMISSION_DENIED` results described above. When the list doesn't name Billing (`billing`), every Billing operation returns `PERMISSION_DENIED`. When the list doesn't name Tracking (`tracking`) or IoT Logic (`iot_logic`), the `tracking` field, the `trackingAssetStates` subscription, and every IoT Logic operation return `PRODUCT_DISABLED` instead, with `productCode` naming the product. Because `tracking` can't be `null`, `data` is then `null` for the whole operation. A change to the products of your workspace takes effect when you get a new token.

## Access token claims

You don't create the token, but its claims decide what the API does. To find out why the API refused a request, decode your token and compare its claims with the table below. Decode the token locally, for example with a JWT library, and don't paste it into an online decoder.

The following example shows a decoded token with only the claims that the API reads. The values are examples:

```json
{
  "iss": "<identity service URL>",
  "sub": "5f0c2a1e-3b7d-4c9a-9e21-7d4b8a6f1c03",
  "exp": 1790000000,
  "email": "alex.morgan@example.com",
  "given_name": "Alex",
  "family_name": "Morgan",
  "organization_id": "019a6b2f-793e-807b-8001-4f52474e0001",
  "workspace_id": "019a6b2f-793e-807b-8001-575350430001",
  "actor_id": "8e2b6d4c-1f3a-4b7e-9c5d-2a6f8e1b3d70",
  "role_ids": ["019a6b2f-793e-807b-8001-49524f4c0001"],
  "product_codes": ["bdr"]
}
```

| Claim             | Type            | Required              | What the API does with it                                                                                                                                                                                                                                                                                        |
| ----------------- | --------------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sub`             | String          | Yes                   | Identifies you.                                                                                                                                                                                                                                                                                                  |
| `email`           | String          | On your first request | The API creates your user record from it. Without it, the first request returns `UNAUTHORIZED`.                                                                                                                                                                                                                  |
| `given_name`      | String          | No                    | Your first name in the user record, read on your first request.                                                                                                                                                                                                                                                  |
| `family_name`     | String          | No                    | Your last name in the user record, read on your first request.                                                                                                                                                                                                                                                   |
| `workspace_id`    | ID              | No                    | The workspace of the request. Without it, the API uses your single active membership. The Billing `my*` operations and the `console` and `iam` operations require it.                                                                                                                                            |
| `organization_id` | ID              | No                    | The organization of the workspace. Your roles apply only for this organization, so without the claim no role applies. The API also uses it to check the workspace with Navixy Console, and refuses a workspace that it doesn't know yet when the claim is missing. The `console` and `iam` operations require it. See [Console overview](console/#who-does-what) and [IAM overview](iam/#who-does-what). |
| `actor_id`        | UUID            | No                    | Your actor ID in Navixy Console. `me.organizations` reads it and returns `PERMISSION_DENIED` without it. To read or change your own roles, send it as `actorId` to the `iam` operations. See [IAM API reference](iam/api-reference/#identifiers).                                                                |
| `role_ids`        | List of strings | Yes                   | Your roles in the workspace. Without roles, the API refuses every field that needs a role.                                                                                                                                                                                                                       |
| `product_codes`   | List of strings | Yes                   | The products of your workspace. Without the claim, the API refuses every Business Data Repository field and mutation that needs a role, and answers `PRODUCT_DISABLED` on `tracking`, `trackingAssetStates`, and the IoT Logic operations.                                                                   |

## Authentication errors

Authentication errors use the same error envelope as every other error of the API. See [Error handling](error-handling.md) for the response structure. In the envelope, `extensions.status` is 401 for `UNAUTHORIZED` and 403 for `PERMISSION_DENIED`. The HTTP status of the response is in the table.

| Cause                                                                                                                                                           | Code                | HTTP status | `detail`                                                     | What to do                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------- | ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| The request has no `Authorization` header. Every field that needs a token, such as `bdr`, returns the error, and nothing under it runs.                        | `UNAUTHORIZED`      | 200         | `Authentication required`                                    | Add the header. See [How to send an access token](authentication.md#how-to-send-an-access-token).                                                                            |
| The token is malformed or expired, was issued by another identity service, or names another audience.                                                           | `UNAUTHORIZED`      | 401         | The reason                                                   | Get a new token and retry once. If a new token fails too, check that it comes from the identity service named in your onboarding details.                                    |
| The token is valid, but the API can't match it to a user. The identity service doesn't know the subject, or the `email` claim is missing on your first request. | `UNAUTHORIZED`      | 401         | `Authenticated principal could not be resolved to a user`    | Check that your account still exists in the identity service. Contact Navixy if it does.                                                                                     |
| The API can't pick the workspace of the request. Each field of Business Data Repository and Tracking returns the error.                                         | `PERMISSION_DENIED` | 200         | The reason                                                   | Read `detail`. See [How the API picks your workspace](authentication.md#how-the-api-picks-your-workspace). If you belong to several workspaces, get a token for one of them. |
| Your workspace doesn't have Tracking or IoT Logic, and the request selects `tracking`, `trackingAssetStates`, or an IoT Logic flow mutation.                    | `PRODUCT_DISABLED`  | 200         | `Product tracking is not enabled for the caller's workspace` | Enable the product for the workspace in Navixy Console, then get a new token.                                                                                                |
| Your roles or the products of your workspace don't allow a mutation or a field that can't be `null`.                                                            | `PERMISSION_DENIED` | 200         | `Access Denied`                                              | Ask an administrator of your organization for a role that grants the action. See [What your roles and products allow](authentication.md#what-your-roles-and-products-allow). |

An `UNAUTHORIZED` response has no `data` field. The following example is the response for a token without `workspace_id`, from a user who belongs to two workspaces:

```json
{
  "errors": [{
    "message": "the caller belongs to more than one workspace and the token names none",
    "path": ["bdr", "workspace"],
    "extensions": {
      "type": "https://api.navixy.com/errors/forbidden",
      "title": "Permission Denied",
      "status": 403,
      "detail": "the caller belongs to more than one workspace and the token names none",
      "instance": "/graphql",
      "code": "PERMISSION_DENIED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-21T14:30:00Z"
    }
  }],
  "data": null
}
```

## Best practices

* Never log a token or commit one to a repository. A token is a credential until it expires.
* Renew the token on a schedule, shortly before it expires, so that a working integration never gets `UNAUTHORIZED`. Don't retry a request with a token that the API rejected: a rejected token stays rejected.
* Read `extensions.code`, not the HTTP status, to tell an expired token from a refused operation. Only `UNAUTHORIZED` (401), `RATE_LIMITED` (429), and a request that the server can't run at all change the HTTP status. See [HTTP status codes](error-handling.md#http-status-codes).
* Don't hardcode a workspace ID in your integration. The API takes the workspace from the `workspace_id` claim, or from your single membership when the token has no such claim.

## See also

* [Getting started](getting-started.md): Send your first authenticated request and activate a device.
* [Error handling](error-handling.md): Learn about the error envelope and every error code.
* [Workspaces](business-data-repository/api-reference/workspaces/): Learn about the workspace entity and the query that returns it.
