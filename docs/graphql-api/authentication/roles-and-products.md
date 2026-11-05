---
description: >-
  How Navixy GraphQL API decides what your app can do: your workspace's
  products, the API key's role, and the records that the role covers.
tags:
  - fundamentals
  - beta
---

# Roles and products

This page explains why Navixy GraphQL API allows some requests and refuses others. Read it when you're planning what your app needs before asking for an API key, when a request returns `PERMISSION_DENIED`, or when a list leaves out a record that you know exists.

Roles decide who can do what in Navixy. A role belongs to an organization. It sets which workspaces its members can access, which products they use, and what they can do with the data — for example, whether they can edit devices. Your organization's administrator creates roles and assigns them to people in Navixy Console. See [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles) in the Console documentation.

An API key also has a role. Your organization's administrator chooses the role when they create the key. The key then gives your app the same access rights as a person who has only that role: the same workspaces, the same products, and the same actions with the same records. The role can be any active role of the organization, including one with Console administration, except the Owner role.

## How to configure access rights

Follow these steps to set up your app's access rights:

1. Decide what your app needs: which products, data types, actions, and whether it needs every record or only some. [Resources and actions by product](#resources-and-actions-by-product) lists what exists.
2. Ask your organization's administrator for a key with a role that grants these rights. See [Role settings in Navixy Console](#role-settings-in-navixy-console). The administrator enables the products for your workspace, creates or chooses the role, and creates the key. See [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Console documentation.
3. Your app exchanges the key for an access token and sends requests. For each request, the API checks three things: your workspace's products, the API key's role, and the records that the role covers. It allows the request only when all three allow it.
4. When the API refuses a request or leaves records out, you find the missing product or access right and ask the administrator to grant it to you. See [How to handle permission errors](#how-to-handle-permission-errors).

If you're the administrator, you set up the role and the key yourself. The key's role can't grant more than your own roles do.

For example, here's what to ask for in three common cases:

| What your app needs to do | Access rights to ask for |
| --- | --- |
| Create assets | **Products**: Business Data Repository. Assets: **View** and **Create**, **All**. Catalog items: **View**, **All**, because a new asset needs the IDs of its type and model. |
| Show the live positions of your vehicles | **Products**: Business Data Repository and Tracking. Assets and devices: **View**, with **All** or only the vehicles that the app shows. |
| Enable a product for your workspace | **Products**: Console, with the **Console administration** feature. See [Console overview](../console/README.md). |

These rows show how to read the sections below. They aren't a list of everything that a key can do.

The sections below follow these steps: they list the access rights that exist, explain each of the three checks and how the role's settings in Navixy Console match them, and say what error the API returns when a check fails. To learn how your app gets an access token, see [Authentication](README.md).

## Resources and actions by product

Each type of data has a resource code, such as `bdr.device`, and a set of actions. A code has the form `product.type`. The sections below list them for each product.

### Business Data Repository

Business Data Repository uses the actions `read`, `create`, `update`, and `delete`. The following table lists each type of data and its resource code:

| Resource | Code |
| --- | --- |
| Devices | `bdr.device` |
| Assets | `bdr.asset` |
| Asset groups | `bdr.asset-group` |
| Geo objects | `bdr.geo-object` |
| Schedules | `bdr.schedule` |
| Inventories | `bdr.inventory` |
| Catalog items that belong to your workspace | `bdr.catalog` |

The audit log, `bdr.audit`, uses only the `read` action. It covers the events of every record in the workspace, even the records that the role doesn't let your app read.

Each kind of operation needs a matching action. A query needs `read`, and a create operation needs `create`. An update operation needs `update` and `read` on the same record, and a delete operation needs `delete` and `read`. A new device or asset needs the IDs of its type and model. Types and models are catalog items, so the role also needs `read` on `bdr.catalog` to find them.

A device that you link to an inventory needs rights on that inventory too. `deviceInventoryLink` needs `read` and `update` on the inventory (`bdr.inventory`). `deviceCreate` with `inventoryId` needs them as well, in addition to `create` on devices. Without them, `deviceCreate` returns `PERMISSION_DENIED` and creates no device. An inventory that the role doesn't let your app read returns the same error as an inventory that doesn't exist.

Without a role that covers Business Data Repository, your app can read only `me` in `bdr`.

### IoT Logic

IoT Logic flows use the actions `read`, `create`, `update`, and `delete`. Their code is `iot_logic.flow`. An action applies to every flow in the workspace: a role can't grant the right to operate on only some flows. A flow shows and changes only the devices that the role lets your app read in Business Data Repository. The other devices remain in the flow. See [Who does what](../iot-logic/#who-does-what) in IoT Logic.

### Tracking and Billing

Tracking and Billing have no resources of their own. Tracking returns data only for the assets and devices that the role lets your app read in Business Data Repository. Billing checks that your workspace has the product and that the role covers Billing.

### Console and IAM

Console and IAM check every request themselves, and the API relays their answer. When one of them refuses a request, the error names it in `dependency` and gives the reason in `dependencyCode`.

* The `console` operations that list your organizations and workspaces need no role. The operations that enable and disable a workspace's products need a role that covers Console with the **Console administration** feature. Without the feature, the list of a workspace's products shows only the products that your role opens. See [Console overview](../console/README.md) and [Console errors](../console/errors.md).
* Every `iam` operation needs a role that covers IAM. Your app can't create, change, or assign a role that grants more than the key's role grants. IAM refuses such a request with `PERMISSION_DENIED` and `GRANT_CEILING_EXCEEDED` in `dependencyCode`. See [IAM API reference](../iam/api-reference/README.md).

## Your workspace's products

Your workspace contains enabled products covered by its plan. The token lists them in its `product_codes` field. See [Access token claims](README.md#access-token-claims). The following table lists the products that this API serves and the code that names each one:

| Product | Code |
| --- | --- |
| [Business Data Repository](../business-data-repository/README.md) | `bdr` |
| [Tracking](../tracking/README.md) | `tracking` |
| [IoT Logic](../iot-logic/README.md) | `iot_logic` |
| [Billing](../billing/README.md) | `billing` |
| [Console](../console/README.md) | `console` |
| [IAM](../iam/README.md) | `iam` |

Console and IAM work for every workspace, whatever its plan. The token can also list products that this API doesn't serve.

The API refuses a product's operations when your workspace doesn't have the product. A product's operations are the queries, mutations, and subscriptions under the product's field. For example, `bdr { devices }` and `bdr { deviceCreate }` are Business Data Repository's operations, `tracking { deviceStates }` and the `trackingAssetStates` subscription are Tracking operations, and `iotLogic { flows }` and `iotLogic { flowCreate }` are IoT Logic operations. The error is `PRODUCT_DISABLED` or `PERMISSION_DENIED`, depending on the product. A `PRODUCT_DISABLED` error names the product in its `productCode` field. A `PERMISSION_DENIED` error doesn't name it. See [How to handle permission errors](#how-to-handle-permission-errors).

A change to your workspace's products takes effect when your app gets a new token. To get a new token, exchange the key again.

## Role of the API key

The API key's role decides what your app can read and change. A key has exactly one role. Your organization's administrator chooses the role when they create the key in Navixy Console, and nobody can change it later. To change what the app can do, the administrator either edits the role or creates a new key with another role. Editing the role changes the access of everyone who has it. If the administrator who created the key has the role too, editing it deletes the key.

A role sets three things:

* The workspaces that it applies to. Your app can exchange the API key for a token only in these workspaces. See [How to choose the workspace](README.md#how-to-choose-the-workspace).
* The products that it covers. When your workspace has a product but the key's role doesn't cover it, the API returns `PERMISSION_DENIED` for the product's operations.
* For each type of data in a product, the actions that it grants and the records that it covers.

A key can't get higher access rights than the administrator who created it. For this reason, Navixy Console deletes the key when the administrator's roles change. The next exchange then fails. See [Key deletion and expiration](README.md#key-deletion-and-expiration).

A change to the role itself, such as a new action, usually takes effect within seconds. Your app doesn't need a new token.

### Role settings in Navixy Console

Your organization's administrator sets up the role on its page in Navixy Console. The following table lists each setting of that page and what the API checks for it:

| Setting in Navixy Console | What the API checks |
| --- | --- |
| **Workspaces** | The workspaces where your app can get a token |
| **Products** | The products whose operations your app can use |
| **View** | The `read` action |
| **Create** | The `create` action |
| **Edit** | The `update` action |
| **Delete** | The `delete` action |
| **All**, for a type of data | Every record of that type, including records created later |
| **Specific**, for a type of data | Only the selected records. See [Access to specific records](#access-to-specific-records) |

The administrator sets up the role in Navixy Console, so when you ask for more access rights, use Console's terminology. For example, ask for the **Edit** right for devices rather than `update` for `bdr.device`. See [Configure what a role allows](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles/manage-roles#configure-what-a-role-allows) in the Console documentation.

## Access to specific records

When a type of data is set to **Specific**, the role covers only the selected records. The API then leaves out the other records without an error:

* A list or a connection returns only the records that the role lets your app read, and its `total` counts only these records.
* A query by ID, such as `device(id:)` or `node(id:)`, returns `null`.
* A list inside another record leaves out the records that your app can't read. For example, the devices of an inventory leave out such devices. A field inside another record that can be `null` returns `null`.
* A mutation on a record that the role doesn't cover returns `PERMISSION_DENIED`.

An empty list or a `null` result doesn't prove that a record doesn't exist. The role might not cover it.

## How to handle permission errors

When the role doesn't allow a mutation or a field that can't be `null`, the API returns a `PERMISSION_DENIED` error with "Access Denied" in `detail`. A field that your app can't read at all returns this error too, for example, `devices` when the role has no `read` on `bdr.device`. Two fields inside other records can't be `null`, so they return this error for a record that your app can't read:

* The `device` of an inventory assignment
* The `asset` of an asset group membership

{% hint style="warning" %}
When the API refuses a top-level list, a connection, or `workspace`, the whole `data` object is `null`, because these fields can't be `null`. The response then has no data for any other field of the request. When you aren't sure that the role allows such a field, send it in a separate request. See [Partial failure](../error-handling.md#partial-failure).
{% endhint %}

The error doesn't name the missing product or action. To find out what's missing, follow these steps:

1. Query `me { products { code } }`. The list has the products that your workspace has and that the API key's role covers. If the product of your request isn't there, look for it in the token's `product_codes` field. If the field has the product, the role doesn't cover it. If the field doesn't have it, the product isn't enabled for your workspace.
2. Query `me { roles { id title } }` to get the key's role. With a role that covers IAM, your app can also read what the role grants. See [Role access](../iam/api-reference/role-access.md).
3. Ask your organization's administrator for a key whose role grants the action, or ask them to enable the product for your workspace.

## See also

* [Authentication](README.md): Learn how your app gets a token, what the token contains, and the errors that authentication returns.
* [Role access](../iam/api-reference/role-access.md): Learn how to read what a role grants.
* [Error handling](../error-handling.md): Learn about the error format and every error code.
* [Manage roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles/manage-roles): Learn how your organization's administrator sets up a role in Navixy Console.
