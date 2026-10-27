---
description: How the Console operations use the shared error codes.
tags:
  - reference
  - beta
---

# Console errors

The Console operations return errors in the shared Navixy GraphQL API format, using the same codes as every other product. Consult this page if you call the `console` operations for your workspace: the queries that list your organizations and workspaces, and the operations that read and change the products of a workspace. It covers where Console differs from the other products in the use of shared codes. For the response structure, the full list of codes, and general scenarios, see [Error handling](../error-handling.md).

A `console` request can be refused twice. The API checks your token and your arguments first. Then Navixy Console decides what you may see and do, and the API relays its answer. The `dependency` field says which of the two refused.

## Refusals relayed from Navixy Console

When Console refuses a request, the API returns the matching shared code and marks the error with `dependency: "console"`. Console's own error code is in `dependencyCode`, and `entityType` names what the request was about. The `message` and `detail` name the operation, such as "Console refused the workspace-product-enable request", and say nothing more, so branch on `dependencyCode`.

<table data-header-hidden><thead><tr><th width="164"></th><th width="182"></th><th></th></tr></thead><tbody><tr><td>Console answers</td><td>Code</td><td><code>dependencyCode</code> on the product operations</td></tr><tr><td>400</td><td><code>VALIDATION_ERROR</code></td><td><code>PRODUCT_UNKNOWN</code>: no product has the code. <code>PRODUCT_NOT_TOGGLABLE</code>: the product can't be turned on or off.</td></tr><tr><td>401, 403</td><td><code>PERMISSION_DENIED</code></td><td><code>WORKSPACE_PRODUCTS_FORBIDDEN</code>, <code>WORKSPACE_OPERATION_FORBIDDEN</code>, <code>PRODUCT_CATALOG_FORBIDDEN</code>: your roles lack the Console administration feature or the action on the workspace. On <code>workspaceProducts</code>, <code>WORKSPACE_PRODUCTS_FORBIDDEN</code> means that your roles give you access to no product installed in the workspace, or that the workspace isn't the one your token names. On <code>members</code>, your roles lack the Console administration feature.</td></tr><tr><td>404</td><td><code>NOT_FOUND</code></td><td><code>WORKSPACE_NOT_FOUND</code>: the workspace doesn't belong to your organization. See <a href="errors.md#missing-workspaces-and-operations">Missing workspaces and operations</a>.</td></tr><tr><td>409</td><td><code>CONFLICT</code></td><td><code>PRODUCT_NOT_COVERED</code>: no active subscription covers the product. <code>PRODUCT_MANAGED_BY_PLAN</code>: the plan requires the product. <code>PRODUCT_NOT_ENABLED</code>: nothing to disable. <code>WORKSPACE_TERMINATING</code>: the workspace is being deleted.</td></tr><tr><td>Anything else or no answer</td><td><code>SERVICE_UNAVAILABLE</code></td><td>None.</td></tr></tbody></table>

Enabling a product that no active subscription of your workspace covers returns this error:

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

### How to handle a refusal from Console

Read `dependencyCode` and act on the reason, not on the shared code. A `CONFLICT` from Console is a refusal in the current state of the product or the workspace, not a concurrent change: it has no `expectedVersion`, and sending the same request again fails the same way. For a product that isn't covered, subscribe your workspace to a plan that includes it (see [Choosing a plan and subscribing](../billing/guides/choosing-a-plan-and-subscribing.md)). For a product that the plan requires, there is nothing to do: it stays enabled. For a missing feature or action, ask the Owner of your organization for a role that has them. To learn in advance whether a change would be refused, read `allowedActions` on the product with [workspaceProducts](api-reference/products.md#workspaceproducts).

## Missing workspaces and operations

The organization and workspace lists cover every organization you belong to, whichever one your token names, but the product operations and `members` act only in the organization named by your token. A product operation on a workspace of another organization returns `NOT_FOUND` with `dependency: "console"` and `entityType: "ConsoleWorkspace"`, not `PERMISSION_DENIED`. Console doesn't say whether the workspace exists.

[workspaceOperation](api-reference/products.md#workspaceoperation) is the exception. For an operation ID that Console doesn't know in your organization, it returns `null`, with no entry in `errors`. A role without the `view` action on the workspace of the operation gets `PERMISSION_DENIED` instead.

The queries that list your organizations and workspaces never return `NOT_FOUND`. An `organizationId` that names no organization you belong to returns an empty page. `members` returns `NOT_FOUND` with `entityType: "ConsoleOrganization"` when Console doesn't know the organization named by your token.

## Errors before Console is called

The API checks your token and your arguments first. These errors have no `dependency` field.

<table><thead><tr><th width="190">Code</th><th>When it occurs</th><th>How to resolve</th></tr></thead><tbody><tr><td><code>PERMISSION_DENIED</code></td><td>The token has no <code>organization_id</code> claim or no <code>workspace_id</code> claim. <code>detail</code> names the missing claim. The Console operations don't fall back to your memberships.</td><td>Request a token issued for the organization and the workspace. See <a href="../authentication.md#access-token-claims">Access token claims</a>.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td><code>workspaceId</code> isn't a workspace ID of this deployment, <code>productId</code> isn't a product ID, the <code>organizationId</code> of <code>accessibleWorkspaces</code> isn't an organization ID of this deployment, or the <code>id</code> of <code>workspaceOperation</code> isn't an operation ID of this deployment. An ID issued in another Navixy region or deployment counts as not valid. <code>field</code> names the argument, for example <code>input.productId</code> or <code>organizationId</code>.</td><td>Send each ID as the API returned it: the workspace ID from <code>accessibleWorkspaces</code>, the organization ID from <code>accessibleOrganizations</code>, the <code>productId</code> from <code>workspaceProducts</code>, and the operation ID from <code>workspaceProductEnable</code> or <code>workspaceProductDisable</code>.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td><code>productCode</code> isn't a valid <a href="../core-api-reference/common.md#code">code</a>. The API refuses the whole request before it runs any field, so the error has no <code>field</code>.</td><td>Send the <code>code</code> as <code>workspaceProducts</code> returned it.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td>The paging arguments break Console's rules: <code>first</code> with <code>last</code> or <code>before</code>, <code>last</code> with <code>after</code>, <code>last</code> without <code>before</code>, <code>before</code> without <code>last</code>, or a page size outside 1 to 100. On <code>members</code>, a cursor that isn't one the query returned.</td><td>Page forward with <code>first</code> and <code>after</code>, backward with <code>last</code> and <code>before</code>. See <a href="api-reference/#how-these-connections-paginate">How these connections paginate</a>.</td></tr><tr><td><code>RATE_LIMITED</code></td><td>You sent more requests than your rate limit allows.</td><td>Wait, then retry. See <a href="../limits.md#request-rate">Request rate</a>.</td></tr></tbody></table>

A request whose token names no organization returns this error on every `console` field it selects:

```json
{
  "errors": [{
    "message": "the token carries no 'organization_id' claim",
    "path": ["console", "workspaceProducts"],
    "extensions": {
      "type": "https://api.navixy.com/errors/forbidden",
      "title": "Permission Denied",
      "status": 403,
      "detail": "the token carries no 'organization_id' claim",
      "instance": "/graphql",
      "code": "PERMISSION_DENIED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-28T10:45:00Z"
    }
  }],
  "data": null
}
```

### How to handle a missing claim

Request a token issued for the organization and the workspace, and send it on every `console` request. A token that works for Business Data Repository can still be refused here: Business Data Repository reads only the `workspace_id` claim and falls back to your single membership, and the Console operations need both claims.

## When Console doesn't respond

`SERVICE_UNAVAILABLE` with `dependency: "console"` means that Console didn't respond to the request: Console is unreachable, this deployment has no Console, or Console couldn't reach Billing to read the catalog or the coverage. Retry after a short pause. If the error persists, contact Navixy with its `traceId`.

The API returns the same error when Console returns an organization ID or an operation ID that isn't valid. On `workspaceProductEnable` and `workspaceProductDisable`, Console has already accepted the change in this case. Don't send the mutation again: read `workspaceProducts` to follow the state of the product.

An organization that you read through `node(id:)` or `nodes(ids:)` is `null` when Console doesn't respond, and the error names its path. The other IDs of the same `nodes(ids:)` call return their results as usual.

## Fields of a Console error

A Console error has the standard fields of [Error handling](../error-handling.md#error-response-structure), with these differences:

* `dependency` is `console` on every error that Console answered or failed to answer, and absent on an error that the API raised itself: a missing claim, a malformed argument, or a paging rule.
* `dependencyCode` is present when Console returned its own code, and absent on `SERVICE_UNAVAILABLE`.
* `entityType` is present on every refusal relayed from Console: `ConsoleWorkspaceProduct` on the product operations, `ConsoleWorkspace` on a workspace that Console doesn't show you, `ConsoleWorkspaceOperation` on an operation, `ConsoleMember` on the members query, and `ConsoleOrganization` when Console doesn't know the organization named by your token. `entityId` is present when Console named the entity.
* `field` is present when the API rejected an argument, for example `input.productId`, and when Console rejected a field that maps to an argument.
* `expectedVersion` and `currentVersion` are never present. A `CONFLICT` from Console isn't a version check.

## See also

* [Error handling](../error-handling.md): Learn the error structure, every code, and the common scenarios across the API.
* [Enabling and disabling a product](guides/enabling-and-disabling-a-product.md): Turn a product on or off and handle the errors of the two mutations.
* [Authentication](../authentication.md): Learn how the token names the organization and the workspace that the Console operations act in.
