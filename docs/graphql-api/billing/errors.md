---
description: >-
  How the Billing operations use the shared error codes: what a missing entity
  returns, which fields a Billing error has, and the errors of the my*
  operations.
tags:
  - reference
  - beta
---

# Billing errors

The Billing operations return errors in the shared Navixy GraphQL API format, using the same codes as every other product. Consult this page if you call the Billing [my\* operations](README.md#purpose-and-capabilities) for your workspace: the operations named with the prefix `my`, such as [mySubscribe](api-reference/subscription.md#mysubscribe) and [myBalance](api-reference/balance.md#mybalance), that read or change the data of the workspace named by your token. It covers where Billing differs from the other products in the use of shared codes. For the response structure, the full list of codes, and general scenarios, see [Error handling](../error-handling.md).

## Missing entities

Billing never returns `NOT_FOUND`. A missing entity is reported in one of 2 ways, depending on where the ID appears.

A query by ID returns `null`, with no entry in `errors`: `node(id:)` returns `null` for a submission of another workspace, and [myInvoice](api-reference/invoice.md#myinvoice) returns `null` for an invoice that doesn't exist or belongs to another workspace. [myBalance](api-reference/balance.md#mybalance) never returns `null`. When a workspace has no balance, the first read opens one at zero.

A mutation that names an entity that doesn't exist returns `VALIDATION_ERROR`. The entity and the ID are in `detail`, and there is no `field`, because a rule of the service rejected the reference rather than the input parsing. Send [mySubscribe](api-reference/subscription.md#mysubscribe) with a plan ID that Billing doesn't know, and the response will be:

```json
{
  "errors": [{
    "message": "Plan not found: 019a6b2f-793e-807b-8001-42504c4e0009",
    "path": ["billing", "mySubscribe"],
    "extensions": {
      "type": "https://api.navixy.com/errors/validation",
      "title": "Validation Error",
      "status": 400,
      "detail": "Plan not found: 019a6b2f-793e-807b-8001-42504c4e0009",
      "instance": "/graphql",
      "code": "VALIDATION_ERROR",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-28T10:45:00Z"
    }
  }],
  "data": { "billing": { "mySubscribe": null } }
}
```

The name in the message is the entity, `Plan`, not the GraphQL type `BillingAvailablePlan`.

### How to handle a missing entity

Treat `null` from a query and `VALIDATION_ERROR` with "not found" in `detail` from a mutation the same way: the entity isn't available to your token. For a plan, read [myAvailablePlans](api-reference/plan.md#myavailableplans) again and send an ID from one of its rows. For an invoice, read [myInvoices](api-reference/invoice.md#myinvoices) again and take the `id` from it.

## IDs of the wrong type

Every Billing ID names its entity type. A plan ID sent where a branch ID belongs, or a UUID that Billing didn't issue, is refused before any lookup, with `VALIDATION_ERROR` and `field` naming the input field, for example, `input.planId`. Read `field` to find the argument to fix. The IDs you send to [mySubscribe](api-reference/subscription.md#mysubscribe) come from [myAvailablePlans](api-reference/plan.md#myavailableplans), so send `planId` and `branchId` as they came, each in its own field.

## Fields of a Billing error

A Billing error has the standard fields of [Error handling](../error-handling.md#error-response-structure), with 2 differences:

* `field` is present when the input itself failed, such as a malformed ID, and absent when a Billing rule rejected the request, such as a plan that the workspace may not take or a product that an active subscription already covers. Branch on `code`, and read `detail` for the reason.
* `entityType` and `entityId` are never present on a Billing error.

## Errors of the my\* operations

The `my*` operations return these codes:

<table><thead><tr><th width="176">Code</th><th width="332">When it occurs</th><th>How to resolve</th></tr></thead><tbody><tr><td><code>PERMISSION_DENIED</code></td><td>The token has no <code>workspace_id</code> claim, or the claim isn't a workspace ID. The <code>my*</code> operations read only that claim and don't fall back to your memberships. Or the roles the token carries give no access to the Billing product; the message is then <code>Access Denied</code>.</td><td>Request a token issued for the workspace. See <a href="../authentication.md#how-the-api-picks-your-workspace">How the API picks your workspace</a> and <a href="../authentication.md#what-your-roles-and-products-allow">What your roles and products allow</a>.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td>The plan or the branch isn't one that <a href="api-reference/plan.md#myavailableplans">myAvailablePlans</a> offers the workspace, the branch belongs to another plan, or an active subscription already covers a product of the plan.</td><td>Read <code>myAvailablePlans</code> and <a href="api-reference/subscription.md#mysubscriptions">mySubscriptions</a> again. To cancel or move a subscription, contact Navixy at <a href="mailto:sales@navixy.com">sales@navixy.com</a>.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td>An ID is malformed or of the wrong type, <code>first</code> is above 100, or, on <code>mySubscribe</code>, the <code>workspace_id</code> claim names a workspace that this deployment doesn't have.</td><td>Read <code>field</code> and <code>detail</code>.</td></tr><tr><td><code>RATE_LIMITED</code></td><td>More than 100 requests in 60 seconds from one user, the subject of the token, or from one client address when the requests carry no token.</td><td>Wait for the seconds in the <code>Retry-After</code> header, then retry. See <a href="../limits.md">Limits</a>.</td></tr></tbody></table>

A request without a workspace claim returns this error on every Billing field it selects:

```json
{
  "errors": [{
    "message": "the token carries no workspace, so there is nothing for this operation to act on",
    "path": ["billing", "myAvailablePlans"],
    "extensions": {
      "type": "https://api.navixy.com/errors/forbidden",
      "title": "Permission Denied",
      "status": 403,
      "detail": "the token carries no workspace, so there is nothing for this operation to act on",
      "instance": "/graphql",
      "code": "PERMISSION_DENIED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-28T10:45:00Z"
    }
  }],
  "data": null
}
```

### How to handle a missing workspace claim

Request a token issued for the workspace whose billing you read.

## See also

* [Error handling](../error-handling.md): Learn the error structure, every code, and the common scenarios across the API.
* [Choosing a plan and subscribing](guides/choosing-a-plan-and-subscribing.md): Subscribe a workspace to a plan and handle the errors of [mySubscribe](api-reference/subscription.md#mysubscribe).
* [Reading balance and statement](guides/reading-balance-and-statement.md): Read a balance and its statement.
* [Reading invoices](guides/reading-invoices.md): Read a workspace's invoices and handle a `null` invoice.
* [Authentication](../authentication.md): Learn how the token names the workspace that every operation acts in.
