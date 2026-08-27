---
description: >-
  Queries and mutations for integration accounts used by API clients, automated
  systems, and third-party service connections.
---

# Integrations

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Integration accounts for API clients, automated systems, and third-party service connections.

## Queries

### integration (query)

Retrieves an integration by its ID.

```graphql
integration(id: ID!): Integration
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the integration to retrieve. |

**Output types:**

<details>

<summary>Integration</summary>

An external system integration with API access.

**Implements:** [Actor](README.md#actor), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The display name of the actor. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace this integration belongs to. |
| `credentialRef` | `String` | A reference to credentials stored in a secure vault. |
| `isActive` | `Boolean!` | Whether this integration is active. |

</details>

---

### integrations

Lists integrations for a workspace.

```graphql
integrations(
    workspaceId: ID!
    filter: IntegrationFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: IntegrationOrder = { field: TITLE, direction: ASC }
  ): IntegrationConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve integrations for. |
| `filter` | `IntegrationFilter` | Filtering options for the returned integrations. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `IntegrationOrder` | The ordering options for the returned integrations. |

**Input types:**

<details>

<summary>IntegrationFilter</summary>

Filtering options for integrations.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isActive` | `Boolean` | Filter by active status. |

</details>

<details>

<summary>IntegrationOrder</summary>

Ordering options for integrations.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [IntegrationOrderField](#integrationorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>IntegrationConnection</summary>

A paginated list of Integration items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IntegrationEdge](#integrationedge)!]! | A list of edges. |
| `nodes` | [[Integration](#integration)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### integrationCreate

Creates a new integration.

```graphql
integrationCreate(
    input: IntegrationCreateInput!
  ): IntegrationPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IntegrationCreateInput!` | The input fields for creating the integration. |

**Input types:**

<details>

<summary>IntegrationCreateInput</summary>

Input for creating a new integration.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the integration. |
| `title` | `String!` | The display name. |
| `credentialRef` | `String` | A reference to credentials in a secure vault. |

</details>

**Output types:**

<details>

<summary>IntegrationPayload</summary>

The result of an integration mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `integration` | [Integration](#integration)! | The created or updated integration. |

</details>

<details>

<summary>Integration (entity)</summary>

An external system integration with API access.

**Implements:** [Actor](README.md#actor), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The display name of the actor. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace this integration belongs to. |
| `credentialRef` | `String` | A reference to credentials stored in a secure vault. |
| `isActive` | `Boolean!` | Whether this integration is active. |

</details>

---

### integrationUpdate

Updates an existing integration.

```graphql
integrationUpdate(
    input: IntegrationUpdateInput!
  ): IntegrationPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IntegrationUpdateInput!` | The input fields for updating the integration. |

**Input types:**

<details>

<summary>IntegrationUpdateInput</summary>

Input for updating an existing integration.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The integration ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `credentialRef` | `String` | The new credential reference. |
| `isActive` | `Boolean` | The new active status. |

</details>

**Output types:**

<details>

<summary>IntegrationPayload</summary>

The result of an integration mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `integration` | [Integration](#integration)! | The created or updated integration. |

</details>

<details>

<summary>Integration (entity)</summary>

An external system integration with API access.

**Implements:** [Actor](README.md#actor), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The display name of the actor. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace this integration belongs to. |
| `credentialRef` | `String` | A reference to credentials stored in a secure vault. |
| `isActive` | `Boolean!` | Whether this integration is active. |

</details>

---

### integrationDelete

Deletes an integration.

```graphql
integrationDelete(
    input: IntegrationDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IntegrationDeleteInput!` | The input fields for deleting the integration. |

**Input types:**

<details>

<summary>IntegrationDeleteInput</summary>

Input for deleting an integration.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The integration ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

</details>

**Output types:**

<details>

<summary>DeletePayload</summary>

The result of a delete mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deletedId` | `ID!` | The ID of the deleted entity. |

</details>

---

## Objects

<a id="integration"></a>

### Integration

An external system integration with API access.

**Implements:** [Actor](README.md#actor), [Node](../common.md#node), [Versioned](../common.md#versioned), [Titled](../common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The display name of the actor. |
| `workspace` | [Workspace](../workspaces/README.md#workspace)! | The workspace this integration belongs to. |
| `credentialRef` | `String` | A reference to credentials stored in a secure vault. |
| `isActive` | `Boolean!` | Whether this integration is active. |

---

<a id="integrationpayload"></a>

### IntegrationPayload

The result of an integration mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `integration` | [Integration](#integration)! | The created or updated integration. |

---

## Inputs

<a id="integrationfilter"></a>

### IntegrationFilter

Filtering options for integrations.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `isActive` | `Boolean` | Filter by active status. |

---

<a id="integrationorder"></a>

### IntegrationOrder

Ordering options for integrations.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [IntegrationOrderField](#integrationorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="integrationcreateinput"></a>

### IntegrationCreateInput

Input for creating a new integration.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the integration. |
| `title` | `String!` | The display name. |
| `credentialRef` | `String` | A reference to credentials in a secure vault. |

---

<a id="integrationupdateinput"></a>

### IntegrationUpdateInput

Input for updating an existing integration.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The integration ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `credentialRef` | `String` | The new credential reference. |
| `isActive` | `Boolean` | The new active status. |

---

<a id="integrationdeleteinput"></a>

### IntegrationDeleteInput

Input for deleting an integration.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The integration ID to delete. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

## Enums

<a id="integrationorderfield"></a>

### IntegrationOrderField

Fields available for ordering integrations.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |

---

## Pagination types

<a id="integrationconnection"></a>

### IntegrationConnection

A paginated list of Integration items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IntegrationEdge](#integrationedge)!]! | A list of edges. |
| `nodes` | [[Integration](#integration)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="integrationedge"></a>

### IntegrationEdge

An edge in the Integration connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Integration](#integration)! | The integration at the end of the edge. |

---
