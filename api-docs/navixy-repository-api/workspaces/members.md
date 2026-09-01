---
description: >-
  Query reference for workspace members: which users belong to a workspace,
  whether each membership is active, and when it was assigned.
---

# Members

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Workspace members represent the relationship between users and workspaces: which user belongs to which workspace, whether the membership is active, and when it was assigned.

## Queries

### member (query)

Retrieves a member by its ID.

```graphql
member(id: ID!): Member
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The ID of the member to retrieve. |

**Output types:**

<details>

<summary>Member</summary>

A user's membership in a workspace.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `user` | [User](../actors/users.md#user)! | The user. |
| `workspace` | [Workspace](README.md#workspace)! | The workspace the user belongs to. |
| `isActive` | `Boolean!` | Whether this membership is active. |
| `assignedAt` | [DateTime](../common.md#datetime)! | The date and time when the user was assigned to this workspace. |

</details>

---

### members

Lists members of a workspace.

```graphql
members(
    workspaceId: ID!
    filter: MemberFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: MemberOrder = { field: ASSIGNED_AT, direction: DESC }
  ): MemberConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve members for. |
| `filter` | `MemberFilter` | Filtering options for the returned members. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `MemberOrder` | The ordering options for the returned members. |

**Input types:**

<details>

<summary>MemberFilter</summary>

Filtering options for members.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `userIds` | `[ID!]` | Filter by users (OR within field). |
| `isActive` | `Boolean` | Filter by active status. |

</details>

<details>

<summary>MemberOrder</summary>

Ordering options for members.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [MemberOrderField](#memberorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>MemberConnection</summary>

A paginated list of Member items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[MemberEdge](#memberedge)!]! | A list of edges. |
| `nodes` | [[Member](#member)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### memberCreate

Adds a user to a workspace as a member.

```graphql
memberCreate(
    input: MemberCreateInput!
  ): MemberPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `MemberCreateInput!` | The input fields for creating the membership. |

**Input types:**

<details>

<summary>MemberCreateInput</summary>

Input for creating a membership.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace ID. |
| `userId` | `ID!` | The user ID to add. |

</details>

**Output types:**

<details>

<summary>MemberPayload</summary>

The result of a membership mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `member` | [Member](#member)! | The created or updated membership. |

</details>

<details>

<summary>Member (entity)</summary>

A user's membership in a workspace.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `user` | [User](../actors/users.md#user)! | The user. |
| `workspace` | [Workspace](README.md#workspace)! | The workspace the user belongs to. |
| `isActive` | `Boolean!` | Whether this membership is active. |
| `assignedAt` | [DateTime](../common.md#datetime)! | The date and time when the user was assigned to this workspace. |

</details>

---

### memberUpdate

Updates a membership.

```graphql
memberUpdate(
    input: MemberUpdateInput!
  ): MemberPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `MemberUpdateInput!` | The input fields for updating the membership. |

**Input types:**

<details>

<summary>MemberUpdateInput</summary>

Input for updating a membership.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The membership ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `isActive` | `Boolean` | The new active status. |

</details>

**Output types:**

<details>

<summary>MemberPayload</summary>

The result of a membership mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `member` | [Member](#member)! | The created or updated membership. |

</details>

<details>

<summary>Member (entity)</summary>

A user's membership in a workspace.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `user` | [User](../actors/users.md#user)! | The user. |
| `workspace` | [Workspace](README.md#workspace)! | The workspace the user belongs to. |
| `isActive` | `Boolean!` | Whether this membership is active. |
| `assignedAt` | [DateTime](../common.md#datetime)! | The date and time when the user was assigned to this workspace. |

</details>

---

### memberRemove

Removes a user from a workspace.

```graphql
memberRemove(
    input: MemberRemoveInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `MemberRemoveInput!` | The input fields for removing the membership. |

**Input types:**

<details>

<summary>MemberRemoveInput</summary>

Input for removing a membership.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The membership ID to remove. |
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

<a id="member"></a>

### Member

A user's membership in a workspace.

**Implements:** [Node](../common.md#node), [Versioned](../common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `user` | [User](../actors/users.md#user)! | The user. |
| `workspace` | [Workspace](README.md#workspace)! | The workspace the user belongs to. |
| `isActive` | `Boolean!` | Whether this membership is active. |
| `assignedAt` | [DateTime](../common.md#datetime)! | The date and time when the user was assigned to this workspace. |

---

<a id="memberpayload"></a>

### MemberPayload

The result of a membership mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `member` | [Member](#member)! | The created or updated membership. |

---

## Inputs

<a id="memberfilter"></a>

### MemberFilter

Filtering options for members.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `userIds` | `[ID!]` | Filter by users (OR within field). |
| `isActive` | `Boolean` | Filter by active status. |

---

<a id="memberorder"></a>

### MemberOrder

Ordering options for members.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [MemberOrderField](#memberorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../common.md#orderdirection)! | The direction to order. |

---

<a id="membercreateinput"></a>

### MemberCreateInput

Input for creating a membership.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace ID. |
| `userId` | `ID!` | The user ID to add. |

---

<a id="memberupdateinput"></a>

### MemberUpdateInput

Input for updating a membership.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The membership ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `isActive` | `Boolean` | The new active status. |

---

<a id="memberremoveinput"></a>

### MemberRemoveInput

Input for removing a membership.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The membership ID to remove. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |

---

## Enums

<a id="memberorderfield"></a>

### MemberOrderField

Fields available for ordering members.

| Value | Description |
| ----- | ----------- |
| `ASSIGNED_AT` | Order by assignment date. |

---

## Pagination types

<a id="memberconnection"></a>

### MemberConnection

A paginated list of Member items.

**Implements:** [Connection](../common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[MemberEdge](#memberedge)!]! | A list of edges. |
| `nodes` | [[Member](#member)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../common.md#countinfo) | The total count of items matching the filter. |

---

<a id="memberedge"></a>

### MemberEdge

An edge in the Member connection.

**Implements:** [Edge](../common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Member](#member)! | The member at the end of the edge. |

---
