---
description: >-
  Read the products of your workspace, enable one that your plan covers, follow
  the change until it's done, and disable a product again through the Console
  API.
tags:
  - guide
  - beta
---

# Enabling and disabling a product

This guide shows how to turn a product on or off for your workspace through the Console API. You will read the products of the workspace with the state of each one, check what you may do with a product, enable it, follow the change until Console reports that it's done, and disable a product again.

Every operation on this page names the workspace by its ID. Navixy Console decides whether you may read or change that workspace, and the API relays its decision.

## Prerequisites

You need an access token that names the organization and the workspace you work in. Without it, every `console` operation is refused with `PERMISSION_DENIED`. See [Authentication](../../authentication.md#access-token-claims) to learn what the token contains.

You also need a role in your organization with the Console administration feature and the `attach_product` and `detach_product` actions on the workspace. The Owner role has both. Roles are set up in Navixy Console.

To find the ID of your workspace and check that your token reaches it, list the workspaces you can reach:

```graphql
query MyWorkspaces {
  console {
    accessibleWorkspaces(first: 10) {
      nodes {
        id
        workspaceName
        organizationDisplayName
        status
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "console": {
      "accessibleWorkspaces": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-575350430001",
            "workspaceName": "TransLog Hamburg",
            "organizationDisplayName": "TransLog GmbH",
            "status": "active"
          }
        ]
      }
    }
  }
}
```

The `id` of the workspace is the `workspaceId` that the product operations take. A `PERMISSION_DENIED` error instead means that the token doesn't name an organization and a workspace. See [Error handling](#error-handling) below.

## How products and coverage work

A product, such as Business Data Repository or Tracking, runs in your workspace once it's enabled there. Console names a product by its code and by its product ID, the same value as in [Billing](../../billing/README.md). [Products](../api-reference/products.md) documents every field. The products that this API serves have the following codes:

| Product | Code |
| --- | --- |
| [Business Data Repository](../../business-data-repository/README.md) | `bdr` |
| [Tracking](../../tracking/README.md) | `tracking` |
| [IoT Logic](../../iot-logic/README.md) | `iot_logic` |

Your workspace can also have products that this API doesn't serve, such as Reports. Their codes are in the list that `workspaceProducts` returns.

Whether you may enable a product depends on coverage. A product is covered when an active subscription of your workspace includes it. Console reads your subscriptions from Billing every time you ask, so a new subscription counts at once. Only a covered product can be enabled. A product that your plan requires is enabled by the plan and can't be disabled.

The `state` of a product says where it stands:

<table><thead><tr><th width="250">State</th><th>Meaning</th></tr></thead><tbody><tr><td><code>AVAILABLE_ON_OTHER_PLANS</code></td><td>No active subscription of your workspace covers the product. Enabling it is refused.</td></tr><tr><td><code>NOT_ENABLED</code></td><td>Covered, but not enabled.</td></tr><tr><td><code>ENABLING</code></td><td>You asked to enable it, and Navixy is setting it up.</td></tr><tr><td><code>ENABLED</code></td><td>Enabled and ready.</td></tr><tr><td><code>DISABLING</code></td><td>You asked to disable it, and Navixy is suspending it.</td></tr><tr><td><code>FAILED</code></td><td>The last change didn't go through. <code>lastFailureCode</code> says why. Enabling or disabling again retries it.</td></tr></tbody></table>

Not covered wins over every other state: a product that was enabled and then lost coverage shows `AVAILABLE_ON_OTHER_PLANS`, with `coverageLostAt` set, and Navixy suspends it. When coverage returns, Navixy enables it again without a request from you.

Each product also lists `allowedActions`: whether you may enable it (`attach_product`) or disable it (`detach_product`) right now, and a `reason` when you can't. An action that your role doesn't grant is absent from the list. Read this list before you call a mutation, because Console refuses a mutation for the same reasons.

## Example scenario: A fleet operator adds Tracking to a workspace

TransLog GmbH runs delivery trucks out of Hamburg. Their workspace is on a plan that requires Business Data Repository and covers Tracking, and the administrator wants Tracking enabled. Later, the operator stops using Tracking in that workspace and disables it again.

{% stepper %}
{% step %}

### Read the products of the workspace

[workspaceProducts](../api-reference/products.md#workspaceproducts) returns every product the workspace has or may have. The list isn't paginated, because a workspace sees a small catalog.

Run this query:

```graphql
query WorkspaceProducts {
  console {
    workspaceProducts(workspaceId: "019a6b2f-793e-807b-8001-575350430001") {
      productId
      code
      displayName
      covered
      isRequired
      coveredByPlanName
      state
      lastFailureCode
      allowedActions {
        action
        allowed
        reason
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "console": {
      "workspaceProducts": [
        {
          "productId": "019a6b2f-793e-807b-8001-425052440001",
          "code": "bdr",
          "displayName": "Business Data Repository",
          "covered": true,
          "isRequired": true,
          "coveredByPlanName": "Fleet Pro",
          "state": "ENABLED",
          "lastFailureCode": null,
          "allowedActions": [
            { "action": "attach_product", "allowed": false, "reason": "managed_by_plan" },
            { "action": "detach_product", "allowed": false, "reason": "managed_by_plan" }
          ]
        },
        {
          "productId": "019a6b2f-793e-807b-8001-425052440002",
          "code": "tracking",
          "displayName": "Tracking",
          "covered": true,
          "isRequired": false,
          "coveredByPlanName": "Fleet Pro",
          "state": "NOT_ENABLED",
          "lastFailureCode": null,
          "allowedActions": [
            { "action": "attach_product", "allowed": true, "reason": null },
            { "action": "detach_product", "allowed": false, "reason": "not_enabled" }
          ]
        },
        {
          "productId": "019a6b2f-793e-807b-8001-425052440003",
          "code": "reports",
          "displayName": "Reports",
          "covered": false,
          "isRequired": false,
          "coveredByPlanName": null,
          "state": "AVAILABLE_ON_OTHER_PLANS",
          "lastFailureCode": null,
          "allowedActions": [
            { "action": "attach_product", "allowed": false, "reason": "not_covered" },
            { "action": "detach_product", "allowed": false, "reason": "not_enabled" }
          ]
        }
      ]
    }
  }
}
```

The workspace sees three products. Business Data Repository is enabled and required by the plan, so neither action is allowed. Tracking is covered by the Fleet Pro plan but not enabled, and `attach_product` is allowed. Reports isn't covered, so it shows `AVAILABLE_ON_OTHER_PLANS` and can't be enabled from here.

Both actions appear on every product because the role of the administrator grants both. A role that grants neither sees an empty `allowedActions` list on every product.
{% endstep %}

{% step %}

### Check what you may do with the product

Before you call a mutation, read the `allowedActions` entry of the action you intend to take. The `reason` values are:

- `not_covered`: No active subscription of your workspace covers the product. Subscribe the workspace to a plan that includes it: see [Choosing a plan and subscribing](../../billing/guides/choosing-a-plan-and-subscribing.md).
- `already_enabled`: The product is `ENABLED` or `ENABLING`. Console accepts a second request and starts one more operation, so don't send it.
- `not_enabled`: The product was never enabled, or disabling was the last request. There is nothing to disable.
- `managed_by_plan`: The plan requires the product, and it has `isRequired: true`. Console refuses to enable or to disable it, with one exception: enabling it again after `FAILED` retries the setup.

In the example, Tracking has `attach_product` allowed with no reason, so the mutation will be accepted.
{% endstep %}

{% step %}

### Enable the product

[workspaceProductEnable](../api-reference/products.md#workspaceproductenable) takes the workspace ID and the product `code` from the list. Run this mutation:

```graphql
mutation EnableTracking {
  console {
    workspaceProductEnable(
      input: {
        workspaceId: "019a6b2f-793e-807b-8001-575350430001"
        productCode: "tracking"
      }
    ) {
      operation {
        id
        workspaceId
        operationType
        status
        createdAt
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "console": {
      "workspaceProductEnable": {
        "operation": {
          "id": "019a6b2f-793e-807b-8001-57534f500001",
          "workspaceId": "019a6b2f-793e-807b-8001-575350430001",
          "operationType": "product_attach",
          "status": "accepted",
          "createdAt": "2026-09-28T10:41:07Z"
        }
      }
    }
  }
}
```

The response confirms that Console accepted the request. It doesn't say whether the product is enabled yet. Console recorded an operation of type `product_attach` with the status `accepted`. Navixy sets up the product afterwards, and meanwhile the product state is `ENABLING`. Keep the operation `id` to follow the change.

A `console` block runs one mutation at a time. To enable two products in one request, give each mutation its own aliased `console` block, such as `tracking: console { ... }` and `reports: console { ... }` (see [Aliases](../../graphql-basics/graphql-tips-and-patterns.md#aliases)). The blocks run in the order you write them.
{% endstep %}

{% step %}

### Follow the change until it's done

[workspaceOperation](../api-reference/products.md#workspaceoperation) returns the operation in full. Read it every few seconds until `status` is no longer `accepted`:

```graphql
query FollowEnableTracking {
  console {
    workspaceOperation(id: "019a6b2f-793e-807b-8001-57534f500001") {
      id
      operationType
      status
      failureReason
      createdAt
      updatedAt
    }
  }
}
```

Response once the product is ready:

```json
{
  "data": {
    "console": {
      "workspaceOperation": {
        "id": "019a6b2f-793e-807b-8001-57534f500001",
        "operationType": "product_attach",
        "status": "completed",
        "failureReason": null,
        "createdAt": "2026-09-28T10:41:07Z",
        "updatedAt": "2026-09-28T10:41:32Z"
      }
    }
  }
}
```

The operation ends as `completed` or `failed`. When it ends as `failed`, `failureReason` is `product_failed`, and the cause is on the product: read `lastFailureCode` with [workspaceProducts](../api-reference/products.md#workspaceproducts). The value `no_response` means that nothing answered within about 12 minutes. Other values name what went wrong in the product.

`workspaceOperation` returns `null` for an operation ID that Console doesn't know in your organization, with no entry in `errors`. Nothing notifies you when the change is done, so read the operation, or the products of the workspace, until it is.
{% endstep %}

{% step %}

### Confirm the product state

Read the products again. Tracking is now `ENABLED`, and its actions changed:

```graphql
query ConfirmTracking {
  console {
    workspaceProducts(workspaceId: "019a6b2f-793e-807b-8001-575350430001") {
      code
      state
      allowedActions {
        action
        allowed
        reason
      }
    }
  }
}
```

Response, showing only the Tracking entry:

```json
{
  "data": {
    "console": {
      "workspaceProducts": [
        {
          "code": "tracking",
          "state": "ENABLED",
          "allowedActions": [
            { "action": "attach_product", "allowed": false, "reason": "already_enabled" },
            { "action": "detach_product", "allowed": true, "reason": null }
          ]
        }
      ]
    }
  }
}
```

Tracking is ready to use in the workspace, and `detach_product` is now an allowed action.
{% endstep %}

{% step %}

### Disable the product

[workspaceProductDisable](../api-reference/products.md#workspaceproductdisable) takes the workspace ID and the `productId` from the list, not the code. Run this mutation:

```graphql
mutation DisableTracking {
  console {
    workspaceProductDisable(
      input: {
        workspaceId: "019a6b2f-793e-807b-8001-575350430001"
        productId: "019a6b2f-793e-807b-8001-425052440002"
      }
    ) {
      operation {
        id
        operationType
        status
        createdAt
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "console": {
      "workspaceProductDisable": {
        "operation": {
          "id": "019a6b2f-793e-807b-8001-57534f500002",
          "operationType": "product_detach",
          "status": "accepted",
          "createdAt": "2026-09-28T14:05:19Z"
        }
      }
    }
  }
}
```

The product state is `DISABLING` until Navixy suspends the product, then `NOT_ENABLED`. Follow the operation the same way as in [Follow the change until it's done](#follow-the-change-until-its-done). Disabling removes user access to the product and keeps its data. Enabling it again restores access.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Tracking was enabled in your workspace and disabled again. You read the products with the state and the allowed actions of each one, asked Console for each change with one mutation, and followed each operation until Console reported that it was done.
{% endhint %}

## Retrying a failed change

When a change ends as `failed`, the product has the state `FAILED`. After a failed enable, both actions are allowed again. After a failed disable, only `attach_product` is allowed, and `detach_product` shows `not_enabled`. Read `lastFailureCode` first. Then call `workspaceProductEnable` again to retry enabling, or `workspaceProductDisable` to give up on the product. Each call clears the failure and starts a new operation. A required product allows only the retry.

## Error handling

The Console operations return errors in the shared format. See [Error handling](../../error-handling.md) for the response structure and the general codes, and [Console errors](../errors.md) for everything specific to Console.

<table><thead><tr><th width="190">Code</th><th>When it occurs</th><th>Resolution</th></tr></thead><tbody><tr><td><code>PERMISSION_DENIED</code></td><td>The token doesn't name your organization and workspace, or Console refused the request because your roles lack the Console administration feature or the action on the workspace.</td><td>Request a token issued for the organization and the workspace. Ask an Owner of your organization for a role with the feature and the action.</td></tr><tr><td><code>NOT_FOUND</code></td><td>The workspace doesn't belong to the organization named by your token.</td><td>Read the workspace ID from <code>accessibleWorkspaces</code> with a token issued for its organization.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td>The workspace ID, the product ID, the operation ID, or the product code is malformed, no product has the code (<code>PRODUCT_UNKNOWN</code>), or the product can't be turned on or off at all (<code>PRODUCT_NOT_TOGGLABLE</code>).</td><td>Read <code>field</code> and <code>dependencyCode</code>. Send the <code>code</code> and <code>productId</code> as <code>workspaceProducts</code> returned them, and the operation <code>id</code> as the mutation returned it.</td></tr><tr><td><code>CONFLICT</code></td><td>Console refused the change in the current state of the product or the workspace: <code>PRODUCT_NOT_COVERED</code>, <code>PRODUCT_MANAGED_BY_PLAN</code>, <code>PRODUCT_NOT_ENABLED</code>, or <code>WORKSPACE_TERMINATING</code>.</td><td>Read <code>dependencyCode</code>. Sending the same request again fails the same way. Read <code>allowedActions</code> before the next attempt.</td></tr><tr><td><code>SERVICE_UNAVAILABLE</code></td><td>Console, or Billing, didn't respond, or Console returned an operation ID that isn't valid.</td><td>Retry a query after a short pause. After a mutation, read <code>workspaceProducts</code> before you send it again, because Console might already have accepted the change. <code>dependency</code> is <code>console</code>.</td></tr></tbody></table>

Enabling a product that no active subscription covers returns this error:

```json
{
  "errors": [{
    "message": "Console refused the workspace-product-enable request",
    "path": ["console", "workspaceProductEnable"],
    "extensions": {
      "type": "https://api.navixy.com/errors/conflict",
      "title": "Concurrent Modification Conflict",
      "status": 409,
      "detail": "Console refused the workspace-product-enable request",
      "instance": "/graphql",
      "code": "CONFLICT",
      "dependency": "console",
      "dependencyCode": "PRODUCT_NOT_COVERED",
      "entityType": "ConsoleWorkspaceProduct",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-28T10:45:00Z"
    }
  }],
  "data": { "console": { "workspaceProductEnable": null } }
}
```

### How to handle a product that isn't covered

Read `dependencyCode`. A `CONFLICT` from Console is a refusal in the current state, not a concurrent change, so sending the same request again fails the same way. Your workspace needs a subscription to a plan that includes the product: see [Choosing a plan and subscribing](../../billing/guides/choosing-a-plan-and-subscribing.md). Then read `workspaceProducts` again. The product shows `NOT_ENABLED` with `attach_product` allowed, and the mutation goes through. To avoid the error, read `allowedActions` before you call the mutation.

## See also

- [Products](../api-reference/products.md): Learn how the products of a workspace, the product catalog, the two mutations, and the operation type work.
- [Console errors](../errors.md): Learn which refusals Console relays and which fields a Console error has.
- [Choosing a plan and subscribing](../../billing/guides/choosing-a-plan-and-subscribing.md): Subscribe your workspace to a plan that covers the product.
- [Authentication](../../authentication.md): Learn how the token names the organization and the workspace that the Console operations act in.
