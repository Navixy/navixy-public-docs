---
description: >-
  Reference for the products of a workspace: the workspaceProducts,
  productCatalog, and workspaceOperation queries, the workspaceProductEnable and
  workspaceProductDisable mutations, and the types they use.
tags:
  - reference
  - beta
---

# Products

The products a workspace may or does have enabled, Console's product catalog, the mutations that enable and disable a product, and the operation Console tracks for each toggle.

## Queries

### workspaceProducts

Every product the workspace may or does have enabled.

```graphql
workspaceProducts(
    workspaceId: ID!
  ): [ConsoleWorkspaceProduct!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to list products for, by its workspace ID. |

**Output types:**

<details>

<summary>ConsoleWorkspaceProduct</summary>

One product a workspace may or does have enabled.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productId` | `ID!` | The product's ID in Billing, relayed by Console: the same value as `BillingProductDomain.id`. |
| `code` | `String!` | The product's code in Billing, the same value as `BillingProductDomain.code`, for example `bdr`. |
| `displayName` | `String!` | Human-readable product name. |
| `description` | `String` | Human-readable product description. Null when Console has none. |
| `covered` | `Boolean!` | Whether the workspace's current plan covers this product. |
| `isDefault` | `Boolean!` | Whether the plan covering this product turned it on by default when this workspace was created. |
| `isRequired` | `Boolean!` | Whether the covering plan requires this product: Console refuses both enabling and disabling it (PRODUCT_MANAGED_BY_PLAN), except re-enabling it after FAILED. |
| `coveredBySubscriptionId` | `ID` | The subscription covering this product, by its ID in Billing. Non-null exactly when `covered` is true: Console's coverage comes only from subscriptions. |
| `coveredByPlanName` | `String` | The name of the plan covering this product, when covered by a plan. |
| `coverageLostAt` | [DateTime](../../core-api-reference/common.md#datetime) | When coverage from a plan or subscription lapsed, if it ever did. Null while still covered, or never covered. |
| `desiredState` | `String` | What Console converges the product to, set by a toggle or by a plan that installs the product. Null when the product was never installed in this workspace. |
| `intentSetAt` | [DateTime](../../core-api-reference/common.md#datetime) | When a user last set desiredState. Null when no user ever has, including a product a plan installed. |
| `observedState` | `String!` | What Console has last observed the product's actual state to be, independent of what was asked for. |
| `generation` | [Long](../../core-api-reference/common.md#long)! | Bumped whenever Console sends a new command for the installation, including on coverage loss; distinguishes a stale observation from a fresh one. |
| `lastFailureCode` | `String` | Console's own code for why the last toggle did not reach the desired state. Null otherwise. |
| `state` | [ConsoleWorkspaceProductState](#consoleworkspaceproductstate)! | The product's current toggle state. |
| `allowedActions` | [[ConsoleActionAffordance](#consoleactionaffordance)!]! | What the caller may do next, and why not when they can't. An action the caller's role does not grant is absent from the list, rather than present with allowed: false. |

</details>

---

### productCatalog

Console's own product catalog.

```graphql
productCatalog(
    activeOnly: Boolean! = true
  ): [ConsoleProductCatalogEntry!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `activeOnly` | `Boolean!` | Restrict to products Console currently offers. Defaults to true. |

**Output types:**

<details>

<summary>ConsoleProductCatalogEntry</summary>

One entry of Console's own product catalog.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The product's ID in Billing, relayed by Console: the same value as `BillingProductDomain.id`. |
| `code` | `String!` | The product's code in Billing, the same value as `BillingProductDomain.code`, for example `bdr`. |
| `displayName` | `String` | Human-readable product name. Null when Console has none. |
| `description` | `String` | Human-readable product description. Null when Console has none. |
| `isActive` | `Boolean!` | Whether the product is currently offered. productCatalog(activeOnly: true) excludes the rest. |
| `isDefault` | `Boolean!` | Whether the default plan turns this product on when a workspace is created. |
| `isRequired` | `Boolean!` | Whether the default plan requires this product. |
| `productUrl` | `String` | The product's user-facing URL, read from nvx4 billing's product domain of this id (BillingProductDomain.productUrl) rather than from Console. Null when billing has none or has no product domain of this id. |
| `internalPath` | `String` | The product's internal path, the same value as `BillingProductDomain.internalPath` in Billing: an absolute http or https URL, or a path that starts with a single `/`. Null when Billing has no path for the product. |

</details>

---

### workspaceOperation

One workspace operation Console tracked, of any type. Null only when Console answers 404: no such
  operation is visible in the caller's organization. A caller without the view action on the
  workspace gets PERMISSION_DENIED instead.

```graphql
workspaceOperation(
    id: ID!
  ): ConsoleWorkspaceOperation
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The operation ID, for example the `id` that `workspaceProductEnable` or `workspaceProductDisable` returned. A value that isn't an operation ID returns `VALIDATION_ERROR` with `field` set to `id`. |

**Output types:**

<details>

<summary>ConsoleWorkspaceOperation</summary>

One workspace operation Console tracked.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The operation ID, as Console issued it. `node(id:)` returns `null` for it, because no type in this API is an operation. |
| `workspaceId` | `ID!` | The workspace the operation acted on, by its workspace ID. |
| `organizationId` | `ID!` | The organization the workspace belongs to: the same value as `ConsoleOrganization.id`. |
| `operationType` | `String!` | Console's own operation-type vocabulary: provision, update, stop, start, delete, product_attach or product_detach. |
| `status` | `String!` | Console's own operation-status vocabulary: accepted, in_progress, completed, failed or cancelled. |
| `idempotencyKey` | [UUID](../../core-api-reference/common.md#uuid) | The idempotency key the request carried, if any. Always null for product_attach/product_detach: Console does not record one for a product toggle. Null on other operation types when none was given. |
| `actorUserId` | [UUID](../../core-api-reference/common.md#uuid) | The user who requested the operation, if any. Null when it was not a user. |
| `actorAgentId` | [UUID](../../core-api-reference/common.md#uuid) | The agent that requested the operation, if any. Null when it was not an agent. |
| `externalOperationId` | `String` | For a product_attach/product_detach operation, Console's own id for the underlying product installation it is tracking. Null on other operation types, or when Console recorded none. |
| `failureReason` | `String` | Console's own code for why the operation failed. Null unless status is failed. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console created the operation record. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console last updated the operation record. |

</details>

---

## Mutations

### workspaceProductEnable

Enables a product on a workspace. Answers once Console accepts the request; the toggle itself
  completes asynchronously — poll `workspaceOperation` or `workspaceProducts` for the outcome.

```graphql
workspaceProductEnable(
    input: ConsoleWorkspaceProductEnableInput!
  ): ConsoleWorkspaceOperationPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `ConsoleWorkspaceProductEnableInput!` | The input fields for enabling the product. |

**Input types:**

<details>

<summary>ConsoleWorkspaceProductEnableInput</summary>

Input for enabling a product on a workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to enable the product on, by its workspace ID. |
| `productCode` | [Code](../../core-api-reference/common.md#code)! | The product's code in Billing, the same value as `BillingProductDomain.code`, for example `bdr`. |

</details>

**Output types:**

<details>

<summary>ConsoleWorkspaceOperationPayload</summary>

The result of toggling a workspace product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `operation` | [ConsoleWorkspaceOperationReceipt](#consoleworkspaceoperationreceipt)! | The receipt Console answered the toggle request with. Follow it with workspaceOperation for the outcome. |

</details>

<details>

<summary>ConsoleWorkspaceOperationReceipt (entity)</summary>

The short receipt Console answers a toggle request with (its 202 body), not the operation read
back in full. The change is accepted and runs asynchronously; follow it with
`console.workspaceOperation(id)` for the outcome. `status` starts as `"accepted"`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The operation ID, as Console issued it. Pass it to `workspaceOperation` to follow the change until it's done. |
| `workspaceId` | `ID!` | The workspace the operation acts on, by its workspace ID. |
| `operationType` | `String!` | Console's own operation-type vocabulary: for a toggle, product_attach or product_detach. |
| `status` | `String!` | Console's own operation-status vocabulary. Starts as \"accepted\" on this receipt. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console created the operation record. |

</details>

---

### workspaceProductDisable

Disables a product on a workspace. Answers once Console accepts the request; the toggle itself
  completes asynchronously — poll `workspaceOperation` or `workspaceProducts` for the outcome.

```graphql
workspaceProductDisable(
    input: ConsoleWorkspaceProductDisableInput!
  ): ConsoleWorkspaceOperationPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `ConsoleWorkspaceProductDisableInput!` | The input fields for disabling the product. |

**Input types:**

<details>

<summary>ConsoleWorkspaceProductDisableInput</summary>

Input for disabling a product on a workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to disable the product on, by its workspace ID. |
| `productId` | `ID!` | The product to disable, by its ID in Billing: the same value as `BillingProductDomain.id`. |

</details>

**Output types:**

<details>

<summary>ConsoleWorkspaceOperationPayload</summary>

The result of toggling a workspace product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `operation` | [ConsoleWorkspaceOperationReceipt](#consoleworkspaceoperationreceipt)! | The receipt Console answered the toggle request with. Follow it with workspaceOperation for the outcome. |

</details>

<details>

<summary>ConsoleWorkspaceOperationReceipt (entity)</summary>

The short receipt Console answers a toggle request with (its 202 body), not the operation read
back in full. The change is accepted and runs asynchronously; follow it with
`console.workspaceOperation(id)` for the outcome. `status` starts as `"accepted"`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The operation ID, as Console issued it. Pass it to `workspaceOperation` to follow the change until it's done. |
| `workspaceId` | `ID!` | The workspace the operation acts on, by its workspace ID. |
| `operationType` | `String!` | Console's own operation-type vocabulary: for a toggle, product_attach or product_detach. |
| `status` | `String!` | Console's own operation-status vocabulary. Starts as \"accepted\" on this receipt. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console created the operation record. |

</details>

---

## Objects

<a id="consoleactionaffordance"></a>

### ConsoleActionAffordance

Whether the caller may take one action on a workspace product, and why not when they can't.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `action` | `String!` | The action this affordance is about, e.g. \"attach_product\" or \"detach_product\". |
| `allowed` | `Boolean!` | Whether the caller may take this action right now. |
| `reason` | `String` | Why not, when allowed is false. Null when allowed is true. |

---

<a id="consoleworkspaceproduct"></a>

### ConsoleWorkspaceProduct

One product a workspace may or does have enabled.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productId` | `ID!` | The product's ID in Billing, relayed by Console: the same value as `BillingProductDomain.id`. |
| `code` | `String!` | The product's code in Billing, the same value as `BillingProductDomain.code`, for example `bdr`. |
| `displayName` | `String!` | Human-readable product name. |
| `description` | `String` | Human-readable product description. Null when Console has none. |
| `covered` | `Boolean!` | Whether the workspace's current plan covers this product. |
| `isDefault` | `Boolean!` | Whether the plan covering this product turned it on by default when this workspace was created. |
| `isRequired` | `Boolean!` | Whether the covering plan requires this product: Console refuses both enabling and disabling it (PRODUCT_MANAGED_BY_PLAN), except re-enabling it after FAILED. |
| `coveredBySubscriptionId` | `ID` | The subscription covering this product, by its ID in Billing. Non-null exactly when `covered` is true: Console's coverage comes only from subscriptions. |
| `coveredByPlanName` | `String` | The name of the plan covering this product, when covered by a plan. |
| `coverageLostAt` | [DateTime](../../core-api-reference/common.md#datetime) | When coverage from a plan or subscription lapsed, if it ever did. Null while still covered, or never covered. |
| `desiredState` | `String` | What Console converges the product to, set by a toggle or by a plan that installs the product. Null when the product was never installed in this workspace. |
| `intentSetAt` | [DateTime](../../core-api-reference/common.md#datetime) | When a user last set desiredState. Null when no user ever has, including a product a plan installed. |
| `observedState` | `String!` | What Console has last observed the product's actual state to be, independent of what was asked for. |
| `generation` | [Long](../../core-api-reference/common.md#long)! | Bumped whenever Console sends a new command for the installation, including on coverage loss; distinguishes a stale observation from a fresh one. |
| `lastFailureCode` | `String` | Console's own code for why the last toggle did not reach the desired state. Null otherwise. |
| `state` | [ConsoleWorkspaceProductState](#consoleworkspaceproductstate)! | The product's current toggle state. |
| `allowedActions` | [[ConsoleActionAffordance](#consoleactionaffordance)!]! | What the caller may do next, and why not when they can't. An action the caller's role does not grant is absent from the list, rather than present with allowed: false. |

---

<a id="consoleproductcatalogentry"></a>

### ConsoleProductCatalogEntry

One entry of Console's own product catalog.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The product's ID in Billing, relayed by Console: the same value as `BillingProductDomain.id`. |
| `code` | `String!` | The product's code in Billing, the same value as `BillingProductDomain.code`, for example `bdr`. |
| `displayName` | `String` | Human-readable product name. Null when Console has none. |
| `description` | `String` | Human-readable product description. Null when Console has none. |
| `isActive` | `Boolean!` | Whether the product is currently offered. productCatalog(activeOnly: true) excludes the rest. |
| `isDefault` | `Boolean!` | Whether the default plan turns this product on when a workspace is created. |
| `isRequired` | `Boolean!` | Whether the default plan requires this product. |
| `productUrl` | `String` | The product's user-facing URL, read from nvx4 billing's product domain of this id (BillingProductDomain.productUrl) rather than from Console. Null when billing has none or has no product domain of this id. |
| `internalPath` | `String` | The product's internal path, the same value as `BillingProductDomain.internalPath` in Billing: an absolute http or https URL, or a path that starts with a single `/`. Null when Billing has no path for the product. |

---

<a id="consoleworkspaceoperation"></a>

### ConsoleWorkspaceOperation

One workspace operation Console tracked.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The operation ID, as Console issued it. `node(id:)` returns `null` for it, because no type in this API is an operation. |
| `workspaceId` | `ID!` | The workspace the operation acted on, by its workspace ID. |
| `organizationId` | `ID!` | The organization the workspace belongs to: the same value as `ConsoleOrganization.id`. |
| `operationType` | `String!` | Console's own operation-type vocabulary: provision, update, stop, start, delete, product_attach or product_detach. |
| `status` | `String!` | Console's own operation-status vocabulary: accepted, in_progress, completed, failed or cancelled. |
| `idempotencyKey` | [UUID](../../core-api-reference/common.md#uuid) | The idempotency key the request carried, if any. Always null for product_attach/product_detach: Console does not record one for a product toggle. Null on other operation types when none was given. |
| `actorUserId` | [UUID](../../core-api-reference/common.md#uuid) | The user who requested the operation, if any. Null when it was not a user. |
| `actorAgentId` | [UUID](../../core-api-reference/common.md#uuid) | The agent that requested the operation, if any. Null when it was not an agent. |
| `externalOperationId` | `String` | For a product_attach/product_detach operation, Console's own id for the underlying product installation it is tracking. Null on other operation types, or when Console recorded none. |
| `failureReason` | `String` | Console's own code for why the operation failed. Null unless status is failed. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console created the operation record. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console last updated the operation record. |

---

<a id="consoleworkspaceoperationreceipt"></a>

### ConsoleWorkspaceOperationReceipt

The short receipt Console answers a toggle request with (its 202 body), not the operation read
back in full. The change is accepted and runs asynchronously; follow it with
`console.workspaceOperation(id)` for the outcome. `status` starts as `"accepted"`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The operation ID, as Console issued it. Pass it to `workspaceOperation` to follow the change until it's done. |
| `workspaceId` | `ID!` | The workspace the operation acts on, by its workspace ID. |
| `operationType` | `String!` | Console's own operation-type vocabulary: for a toggle, product_attach or product_detach. |
| `status` | `String!` | Console's own operation-status vocabulary. Starts as \"accepted\" on this receipt. |
| `createdAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When Console created the operation record. |

---

<a id="consoleworkspaceoperationpayload"></a>

### ConsoleWorkspaceOperationPayload

The result of toggling a workspace product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `operation` | [ConsoleWorkspaceOperationReceipt](#consoleworkspaceoperationreceipt)! | The receipt Console answered the toggle request with. Follow it with workspaceOperation for the outcome. |

---

## Inputs

<a id="consoleworkspaceproductenableinput"></a>

### ConsoleWorkspaceProductEnableInput

Input for enabling a product on a workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to enable the product on, by its workspace ID. |
| `productCode` | [Code](../../core-api-reference/common.md#code)! | The product's code in Billing, the same value as `BillingProductDomain.code`, for example `bdr`. |

---

<a id="consoleworkspaceproductdisableinput"></a>

### ConsoleWorkspaceProductDisableInput

Input for disabling a product on a workspace.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to disable the product on, by its workspace ID. |
| `productId` | `ID!` | The product to disable, by its ID in Billing: the same value as `BillingProductDomain.id`. |

---

## Enums

<a id="consoleworkspaceproductstate"></a>

### ConsoleWorkspaceProductState

Console's own workspace-product toggle state (`WorkspaceProductState.resolve`), derived from whether
a subscription the workspace holds covers the product and what Console last asked for (desired)
versus what it has last observed.

| Value | Description |
| ----- | ----------- |
| `AVAILABLE_ON_OTHER_PLANS` | No active subscription of the workspace covers this product; Console does not check that another plan offers it. |
| `NOT_ENABLED` | Covered by a subscription the workspace holds, but not toggled on. |
| `ENABLING` | Covered, desired is enabled, and Console has not yet observed it ready — including a product a covering plan turns on at workspace creation. |
| `ENABLED` | Covered, desired is enabled, and Console has observed it ready. |
| `DISABLING` | A workspaceProductDisable is in flight. |
| `FAILED` | The last toggle did not reach the desired state; see ConsoleWorkspaceProduct.lastFailureCode. |

---

## See also

* [Pagination](../../pagination.md)
* [Error handling](../../error-handling.md)
* [Limits](../../limits.md)
