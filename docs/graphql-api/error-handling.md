---
description: Understand Navixy GraphQL API errors based on RFC 9457 format
tags:
  - reference
  - beta
---

# Error handling

When an operation fails, the API returns an error response with details to help you understand what went wrong and how to fix it. All errors follow the [RFC 9457 Problem Details](https://www.rfc-editor.org/rfc/rfc9457.html) format, providing both human-readable messages and machine-readable codes.

## Error response structure

Errors are returned in the standard GraphQL format with additional details in the `extensions` field:

```json
{
  "errors": [{
    "message": "Device not found: 019a6a3f-...",
    "locations": [{ "line": 2, "column": 3 }],
    "path": ["bdr", "deviceUpdate"],
    "extensions": {
      // RFC 9457 standard fields
      "type": "https://api.navixy.com/errors/not-found",
      "title": "Resource Not Found",
      "status": 404,
      "detail": "Device not found: 019a6a3f-...",
      "instance": "/graphql",
      
      // Navixy-specific fields
      "code": "NOT_FOUND",
      "field": "input.id",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2025-01-15T14:30:00Z"
    }
  }],
  "data": { "bdr": { "deviceUpdate": null } }
}
```

### Standard fields

Every error includes these RFC 9457 standard fields:

<table><thead><tr><th width="100">Field</th><th width="100">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>type</code></td><td>URI</td><td>A stable identifier for the error type, in URL form.</td></tr><tr><td><code>title</code></td><td>String</td><td>Summary of the problem type. Stays the same for all errors of this type.</td></tr><tr><td><code>status</code></td><td>Integer</td><td>HTTP status code (e.g., 400, 404, 409).</td></tr><tr><td><code>detail</code></td><td>String</td><td>Human-readable explanation specific to this occurrence of the problem.</td></tr><tr><td><code>instance</code></td><td>URI</td><td>The request path that caused the error. Useful for logs or support tickets.</td></tr></tbody></table>

### Navixy-specific fields

<table><thead><tr><th width="181.60003662109375">Field</th><th>Description</th></tr></thead><tbody><tr><td><code>code</code></td><td>Machine-readable error code for use in your application logic.</td></tr><tr><td><code>traceId</code></td><td>Distributed tracing ID (32 hex characters). Equals the trace ID of the <code>traceparent</code> header when the request sends one; otherwise the API generates it. Present on every error, including errors on requests without a token.</td></tr><tr><td><code>timestamp</code></td><td>When the error occurred (<a href="https://www.iso.org/iso-8601-date-and-time-format.html"><code>ISO 8601 format</code></a>).</td></tr><tr><td><code>field</code></td><td>For validation errors: the field that failed validation (e.g., <code>input.title</code>).</td></tr><tr><td><code>entityType</code></td><td>Present when a custom field references an entity that no longer exists: the type of that entity (for example <code>Device</code>). Also present on a refusal relayed from Navixy Console: what the request was about. Also present on <code>IN_USE</code>: the type of the record that the delete named, for example <code>ASSET_TYPE</code>.</td></tr><tr><td><code>entityId</code></td><td>Present only with <code>entityType</code>: the ID of that entity.</td></tr><tr><td><code>dependency</code></td><td>Present when the error comes from a system that the API relays, such as Navixy Console (<code>console</code>): on <code>SERVICE_UNAVAILABLE</code>, and on a refusal that system answered with. See <a href="error-handling.md#refusals-relayed-from-navixy-console">Refusals relayed from Navixy Console</a>.</td></tr><tr><td><code>dependencyCode</code></td><td>Present with <code>dependency</code> when that system answered with its own error code, for example <code>PRODUCT_NOT_COVERED</code>.</td></tr><tr><td><code>expectedVersion</code></td><td>Present only when <code>CONFLICT</code> comes from a version check.</td></tr><tr><td><code>currentVersion</code></td><td>Present only when <code>CONFLICT</code> comes from a version check.</td></tr><tr><td><code>allowedValues</code></td><td>For enum validation errors: the list of valid options.</td></tr><tr><td><code>violations</code></td><td>For IoT Logic flow validation errors: every rule that the flow breaks, with the input fields to fix. See <a href="iot-logic/flow-validation-errors.md">Flow validation errors</a>.</td></tr><tr><td><code>productCode</code></td><td>Present on <code>PRODUCT_DISABLED</code>: the product that your workspace doesn't have, for example <code>tracking</code>.</td></tr><tr><td><code>blockers</code></td><td>Present on <code>IN_USE</code>: up to five entries, one for each kind of record and each field of it that still points at the record you tried to delete, as <code>{ entityType, path, ids }</code>. <code>path</code> is that field, for example <code>type</code> or <code>customFields.&lt;code&gt;</code>, and <code>ids</code> lists up to 20 of them that your role can read. An empty list means that such records exist but you can't read them. See <a href="error-handling.md#in-use-409">In use (409)</a>.</td></tr><tr><td><code>hasMoreBlockers</code></td><td>Present on <code>IN_USE</code>: <code>true</code> when more blockers exist than the five in <code>blockers</code>, <code>false</code> when <code>blockers</code> is the whole list.</td></tr></tbody></table>

## Error codes

Every error has a code in `extensions.code`. The table lists every code that the API returns. A new version of the API can add codes, so handle a code that isn't in the table as a generic failure.

<table><thead><tr><th width="190.79998779296875">Code</th><th width="69.39996337890625">Status</th><th width="263">Type URI</th><th>When it occurs</th></tr></thead><tbody><tr><td><code>UNAUTHORIZED</code></td><td>401</td><td><code>.../errors/unauthorized</code></td><td>The request has no access token, or the token is invalid, expired, or from an untrusted issuer. With an invalid or expired token, the HTTP status is 401 and the response has no <code>data</code>. Without a token, the HTTP status is 200 and each field that needs a token is <code>null</code>. A tracking stream also ends with this code when its token expires.</td></tr><tr><td><code>PERMISSION_DENIED</code></td><td>403</td><td><code>.../errors/forbidden</code></td><td>You don't have permission to perform this action. For example, your role doesn't allow the operation, or Business Data Repository or Billing isn't enabled for your workspace. See <a href="authentication.md#what-your-roles-and-products-allow">What your roles and products allow</a>.</td></tr><tr><td><code>NOT_FOUND</code></td><td>404</td><td><code>.../errors/not-found</code></td><td>A mutation names a record that doesn't exist, was deleted, or belongs to another workspace. For example, <code>assetGroupItemsAdd</code> with an unknown asset ID. A query by ID returns <code>null</code> instead.</td></tr><tr><td><code>VALIDATION_ERROR</code></td><td>400</td><td><code>.../errors/validation</code></td><td>Input data failed validation. For example, <code>first</code> above 100, an ID of the wrong type, or a query sent over the WebSocket. <code>field</code> names the input when the error is about one value.</td></tr><tr><td><code>CONFLICT</code></td><td>409</td><td><code>.../errors/conflict</code></td><td>The write conflicted with another request. With <code>expectedVersion</code> and <code>currentVersion</code> present, fetch the entity again and retry with its current version, see <a href="optimistic-locking.md">Optimistic locking</a>. Without them, retry once after a short pause, then stop and report the failure with its <code>traceId</code>. Console and IAM also return this code when they refuse a request in the current state of the record, for example IAM when you revoke the last Owner role. See <a href="error-handling.md#refusals-relayed-from-navixy-console">Refusals relayed from Navixy Console</a>.</td></tr><tr><td><code>DUPLICATE</code></td><td>409</td><td><code>.../errors/duplicate</code></td><td>A value that must be unique already exists. For example, a device identifier that another device has, or the code of an existing type.</td></tr><tr><td><code>IN_USE</code></td><td>409</td><td><code>.../errors/in-use</code></td><td>A delete was refused because other records still point at the record. <code>blockers</code> names them. See <a href="error-handling.md#in-use-409">In use (409)</a>.</td></tr><tr><td><code>RATE_LIMITED</code></td><td>429</td><td><code>.../errors/rate-limited</code></td><td>Too many requests, or too many open tracking subscriptions. For example, more than 100 requests in 60 seconds. See <a href="limits.md">Limits</a>.</td></tr><tr><td><code>QUERY_TOO_COMPLEX</code></td><td>400</td><td><code>.../errors/query-too-complex</code></td><td>The query selects more fields than the complexity limit allows. See <a href="limits.md#query-complexity">Query complexity</a>.</td></tr><tr><td><code>QUERY_TOO_DEEP</code></td><td>400</td><td><code>.../errors/query-too-deep</code></td><td>The query nests deeper than the depth limit allows. See <a href="limits.md#query-depth">Query depth</a>.</td></tr><tr><td><code>PRODUCT_DISABLED</code></td><td>403</td><td><code>.../errors/product-disabled</code></td><td>Your workspace doesn't have the product the field belongs to. <code>productCode</code> names it. Tracking and IoT Logic return this code.</td></tr><tr><td><code>SERVICE_UNAVAILABLE</code></td><td>503</td><td><code>.../errors/service-unavailable</code></td><td>A service that the API calls is unavailable, for example while Tracking starts. <code>dependency</code> names it when it's known, for example <code>console</code>, <code>iam</code>, <code>tracking</code>, or <code>tracking-timeline</code>. Retry after a short delay.</td></tr><tr><td><code>INTERNAL_ERROR</code></td><td>500</td><td><code>.../errors/internal</code></td><td>An unexpected server failure. Report it with its <code>traceId</code>.</td></tr></tbody></table>

An error from the GraphQL validation of the request itself, such as an unknown field or an argument of the wrong type, has the code `VALIDATION_ERROR` too.

### Refusals relayed from Navixy Console

The `console` operations call Navixy Console, and Console decides what a caller may see and do. When Console refuses a request, the API answers with the matching error code and marks the error with `dependency: "console"`. When Console's answer carries its own error code, `dependencyCode` holds it.

<table><thead><tr><th width="190">Console answers</th><th width="200">Code</th><th>Examples of Console's own codes</th></tr></thead><tbody><tr><td>400</td><td><code>VALIDATION_ERROR</code></td><td><code>PRODUCT_UNKNOWN</code>, <code>PRODUCT_NOT_TOGGLABLE</code></td></tr><tr><td>401, 403</td><td><code>PERMISSION_DENIED</code></td><td></td></tr><tr><td>404</td><td><code>NOT_FOUND</code></td><td></td></tr><tr><td>409</td><td><code>CONFLICT</code></td><td><code>PRODUCT_NOT_COVERED</code>, <code>PRODUCT_MANAGED_BY_PLAN</code>, <code>PRODUCT_NOT_ENABLED</code>, <code>WORKSPACE_TERMINATING</code></td></tr><tr><td>Anything else, or no answer</td><td><code>SERVICE_UNAVAILABLE</code></td><td></td></tr></tbody></table>

A `CONFLICT` from Console is Console refusing the request in its current state, not a concurrent modification. It carries no `expectedVersion`, and retrying the request unchanged fails the same way. To learn in advance whether an action would be refused, read `allowedActions` on the product: see [Products](console/api-reference/products.md).

## HTTP status codes

Navixy GraphQL API follows the [GraphQL-over-HTTP specification](https://graphql.github.io/graphql-over-http/draft/), which means HTTP status codes reflect transport-level results, not domain-level errors.

<table><thead><tr><th width="128.4000244140625">HTTP Status</th><th>Meaning</th></tr></thead><tbody><tr><td>200</td><td>Request processed successfully. Check the <code>errors</code> array for any domain-level failures.</td></tr><tr><td>400</td><td>Invalid HTTP or GraphQL request (parse or validation error).</td></tr><tr><td>401</td><td>Authentication required or invalid.</td></tr><tr><td>429</td><td>Rate limit exceeded.</td></tr><tr><td>5xx</td><td>Infrastructure or system error.</td></tr></tbody></table>

All business logic errors (such as `NOT_FOUND`, `CONFLICT`, or `VALIDATION_ERROR`) return HTTP 200 with the semantic status code in `extensions.status`. Use `extensions.code` for programmatic error handling, not the HTTP status.

Four cases change the HTTP status. A token that the API can't accept returns 401. A request over the request rate returns 429. A request that the API can't run at all returns 400 when your `Accept` header is `application/graphql-response+json`, and 200 when it's `application/json`. This covers parse and validation errors, `QUERY_TOO_COMPLEX`, and `QUERY_TOO_DEEP`. A body that isn't a GraphQL request returns 400 whatever your `Accept` header says, with or without a token: a body that isn't JSON, a JSON value that isn't an object, an object without `query`, or `variables` or `extensions` that aren't objects. The error has the code `VALIDATION_ERROR`, and its `detail` names the expected shape.

## Partial failure

One request can partly succeed. The response then has both `data` and `errors`. The field that failed is `null` in `data`, and the `path` of its error names that field. Every other field returns its value as usual. So check `errors` in every response, even when `data` isn't empty.

A failure can make more than one field `null`. When a field that can't be `null` fails, the API sets its parent to `null`. If the parent can't be `null` either, the API sets the next parent to `null`, up to the first field that can be `null`. The product fields at the root, such as `bdr` and `tracking`, can't be `null`. The lists and connections inside them, `workspace`, and `me` can't be `null` either. When one of these fields fails, for example with `PERMISSION_DENIED`, the whole `data` object is `null`, and the request loses every other field and alias. IoT Logic mutations return payloads that can't be `null`, so a failed IoT Logic mutation also makes `data` `null`.

{% hint style="warning" %}
When a request includes a root list (a list directly inside a product field such as `bdr`), `workspace`, or an IoT Logic mutation, send that field in a separate request. If it fails, it makes `data` `null` and removes the results of every other field.
{% endhint %}

Operations that work on several items at once handle a failed item in one of three ways:

| Behavior | Operations | What you see |
| --- | --- | --- |
| The whole call is refused, nothing changes | `assetGroupItemsAdd`, and the `assetIds` of `assetGroupCreate` and `assetGroupUpdate`, when an asset doesn't exist or belongs to another workspace. `flowCreate` and `flowUpdate` when the flow breaks a rule | One error. `NOT_FOUND` lists the bad asset IDs in `detail`. A flow error lists the broken rules it found in `violations` |
| Items that need no change are skipped without an error | `assetGroupItemsAdd` for assets already in the group. `assetGroupItemsRemove` for assets that aren't in it | No error. Read the group's `currentAssets` to see the result |
| Each item gets its own result | `nodes(ids:)` and the tracking `deviceStates` query | `null` in the place of each ID that doesn't exist or that your role can't read |

A delete that is refused with `IN_USE` changes nothing: see [Deleting records](deletion.md#when-a-delete-is-refused).

Several mutations in one request run one after another, each in its own aliased `bdr` block. Each block succeeds or fails on its own, so a failed block doesn't undo the blocks before it. See [Batch mutations](graphql-basics/graphql-tips-and-patterns.md#batch-mutations).

## Common error scenarios

### Unauthorized (401)

Returned when the request carries a token that is malformed, expired, or wasn't issued by the identity service that the API trusts. The check runs before the query, so the response has no `data` field and the HTTP status is 401, unlike the 200 that business logic errors return. A request with no token at all is not an HTTP 401: it reaches the query, and every field that needs a token returns `UNAUTHORIZED` with `detail` `Authentication required` inside an HTTP 200 response. The `bdr`, `billing`, `tracking` and `nodes` fields can't be null, so the whole `data` field is null then; `node` on its own returns null. Introspection without a token returns `UNAUTHORIZED` with `detail` `Introspection requires authentication` and no part of the schema. See [Authentication](authentication.md) for how to obtain a token.

```json
{
  "errors": [{
    "message": "Jwt expired at 2026-09-02T09:05:00Z",
    "extensions": {
      "type": "https://api.navixy.com/errors/unauthorized",
      "title": "Unauthorized",
      "status": 401,
      "detail": "Jwt expired at 2026-09-02T09:05:00Z",
      "instance": "/graphql",
      "code": "UNAUTHORIZED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-02T09:07:12Z"
    }
  }]
}
```

**How to handle:** Obtain a new token and retry the request once. Don't retry with the same token: a rejected token stays rejected, so a retry loop only adds latency. Refresh on a schedule instead, shortly before the token expires, so a working integration never sees this error. If the token is fresh and the error persists, check that it was issued by the identity service that the API trusts.

### Validation error (400)

Returned when input data fails validation: a required field is missing, a value is out of range, or a string exceeds the maximum length.

```json
{
  "errors": [{
    "message": "Field 'title' must not be empty",
    "path": ["bdr", "deviceCreate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/validation",
      "title": "Validation Error",
      "status": 400,
      "detail": "Field 'title' must not be empty",
      "instance": "/graphql",
      "code": "VALIDATION_ERROR",
      "field": "input.title",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2025-01-15T14:30:00Z"
    }
  }]
}
```

**How to handle:** Check the `field` to identify which input failed, and `detail` for the specific requirement.

### Permission denied (403)

The API returns this error in these cases:

* The API could not tell which workspace the request reads or changes. The `detail` names the reason, for example `the token names no workspace` or `the token names a Console workspace that was terminated`. See [Authentication § How the API picks your workspace](authentication.md#how-the-api-picks-your-workspace) for the rule the API applies.
* You're authenticated, but your roles don't allow the operation. See [What your roles and products allow](authentication.md#what-your-roles-and-products-allow).
* Your workspace doesn't have Business Data Repository or Billing, and the request uses it.

```json
{
  "errors": [{
    "message": "Access Denied",
    "path": ["bdr", "deviceDelete"],
    "extensions": {
      "type": "https://api.navixy.com/errors/forbidden",
      "title": "Permission Denied",
      "status": 403,
      "detail": "Access Denied",
      "instance": "/graphql",
      "code": "PERMISSION_DENIED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2025-01-15T14:30:00Z"
    }
  }]
}
```

**How to handle:** Check that the request carries a token. If it does, read `detail`: a workspace reason means that you need a token issued for one workspace, see [Authentication](authentication.md). Otherwise the operation isn't allowed for your account. An administrator of your organization assigns roles in Navixy Console or through the [IAM operations](iam/), so ask them for a role that allows the operation.

### Entity not found (404)

Returned by a mutation when the entity you name doesn't exist in your workspace or has been deleted. A by-id query never returns this error: it answers `null` instead, see below.

```json
{
  "errors": [{
    "message": "Device not found: 019a6b2f-793e-807b-8001-444556430031",
    "path": ["bdr", "deviceUpdate"],
    "locations": [{ "line": 1, "column": 9 }],
    "extensions": {
      "type": "https://api.navixy.com/errors/not-found",
      "title": "Resource Not Found",
      "status": 404,
      "detail": "Device not found: 019a6b2f-793e-807b-8001-444556430031",
      "code": "NOT_FOUND",
      "field": "input.id",
      "traceId": "0af7651916cd43dd8448eb211c80319c"
    }
  }],
  "data": { "bdr": { "deviceUpdate": null } }
}
```

**How to handle:** Check that the ID is correct. If you're using an ID from a previous response, the entity may have been deleted. A by-id query such as `device(id:)` returns `null` without an error when the ID belongs to another workspace or to nothing at all, so treat `null` and `NOT_FOUND` the same way: the entity is not available to your token. Billing mutations report a missing entity as `VALIDATION_ERROR` instead: see [Billing errors](billing/errors.md).

### Version conflict (409)

Returned when the entity was modified by another request since you last fetched it. See [Optimistic locking](optimistic-locking.md) for details.

```json
{
  "errors": [{
    "message": "Version conflict: expected 5, found 6",
    "path": ["bdr", "deviceUpdate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/conflict",
      "title": "Concurrent Modification Conflict",
      "status": 409,
      "detail": "Version conflict: expected 5, found 6",
      "instance": "/graphql",
      "code": "CONFLICT",
      "field": "input.version",
      "expectedVersion": 5,
      "currentVersion": 6,
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2025-01-15T14:30:00Z"
    }
  }]
}
```

**How to handle:** Fetch the entity again to get the current version, merge your changes if needed, and retry with the new version in your update input.

A `CONFLICT` without `expectedVersion` and `currentVersion` is not a version mismatch. It comes from a concurrent write that was rolled back, a device reassignment that raced another change, or a data-integrity conflict, and the code alone doesn't say which. Retry once after a short pause. If the error persists, stop and report the failure along with its `traceId`.

### Duplicate (409)

Returned when you try to create or update an entity with a value that must be unique but already exists, such as a device identifier, user email, or catalog item code.

```json
{
  "errors": [{
    "message": "A device identifier with this type, value, and namespace already exists",
    "path": ["bdr", "deviceCreate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/duplicate",
      "title": "Duplicate Entry",
      "status": 409,
      "detail": "A device identifier with this type, value, and namespace already exists",
      "instance": "/graphql",
      "code": "DUPLICATE",
      "field": "input.identifier",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2025-01-15T14:30:00Z"
    }
  }]
}
```

**How to handle:** The `detail` names the uniqueness rule that was violated, and `field` points at the input that holds the conflicting value. Either use a different value or query for the existing entity if you need to update it instead.

### In use (409)

Returned when you delete a record that other records still point at, for example an asset type that an asset has, or a device that a required custom field holds. Nothing is deleted. [Deleting records](deletion.md#when-a-delete-is-refused) lists every reference that refuses a delete.

```json
{
  "errors": [{
    "message": "Cannot delete Asset Type 019a6b2f-793e-807b-8001-434154500002: it is still referenced by Asset (type)",
    "path": ["bdr", "assetTypeDelete"],
    "extensions": {
      "type": "https://api.navixy.com/errors/in-use",
      "title": "Resource In Use",
      "status": 409,
      "detail": "Cannot delete Asset Type 019a6b2f-793e-807b-8001-434154500002: it is still referenced by Asset (type)",
      "instance": "/graphql",
      "code": "IN_USE",
      "entityType": "ASSET_TYPE",
      "entityId": "019a6b2f-793e-807b-8001-434154500002",
      "blockers": [
        {
          "entityType": "ASSET",
          "path": "type",
          "ids": ["019a6b2f-793e-807b-8001-415353540001"]
        }
      ],
      "hasMoreBlockers": false,
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2025-01-15T14:30:00Z"
    }
  }]
}
```

**How to handle:** Read `blockers`. Each entry names a kind of record, the field that points at the record you tried to delete, and up to 20 such records that your role can read. The list has at most five entries; when `hasMoreBlockers` is `true`, more blockers remain after you clear these. Change or remove those references through the records' own mutations, then delete again. [How to clear the blockers of a delete](deletion.md#how-to-clear-the-blockers-of-a-delete) gives the change for each `path`. An entry with an empty `ids` list means that your role can't read the records that block the delete.

## Best practices

1. Always check for errors. Inspect the `errors` array in every response, even when the HTTP status is 200 (GraphQL can return partial data with errors).
2. Use `code` for logic, `detail` for display. Base your error handling on `code`, not on `detail`. New codes can appear, and a failure can move to a different code, so handle an unknown code as a generic failure. The `detail` field is human-readable and suitable for showing to users.
3. Log the `traceId`. When users report issues, the `traceId` helps the support team quickly locate the relevant logs. Include it in your bug reports.
4. Handle conflicts gracefully. In collaborative applications, version conflicts are expected. Implement retry logic or prompt users to review changes.

## See also

* [Authentication](authentication.md): Obtain, refresh, and send the access token that every request needs
* [Optimistic locking](optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
* [Limits](limits.md): Request rate, subscription, pagination, and input size limits, and the errors they return
* [GraphQL basics](graphql-basics/README.md): Learn GraphQL fundamentals, from queries and mutations to the type system
