---
description: >-
  Reference for the workspaces a caller can reach through Navixy Console: the
  accessibleWorkspaces query and the ConsoleWorkspace type with its connection.
tags:
  - reference
  - beta
---

# Workspaces

The workspaces the caller can access, as Navixy Console reports them, with the organization and the zone each one belongs to.

## Queries

### accessibleWorkspaces

Workspaces the caller can access, optionally narrowed to one organization.

```graphql
accessibleWorkspaces(
    organizationId: ID
    first: Int
    after: String
    last: Int
    before: String
  ): ConsoleWorkspaceConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `organizationId` | `ID` | Only the workspaces of this organization. Leave it out to list the workspaces of every organization you belong to. A value that isn't an organization ID returns `VALIDATION_ERROR` with `field` set to `organizationId`. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). Requires `before` — Console pages backward only from a cursor. |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). Requires `last`. |

**Output types:**

<details>

<summary>ConsoleWorkspaceConnection</summary>

A page of workspaces the caller can access.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ConsoleWorkspaceEdge](#consoleworkspaceedge)!]! |  |
| `nodes` | [[ConsoleWorkspace](#consoleworkspace)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter, with precision APPROXIMATE: Console counts every workspace, including any whose ID isn't a workspace ID of this deployment and which this query leaves out of `edges` and `nodes`, so the count can exceed what the connection returns. |

</details>

---

## Objects

<a id="consoleworkspace"></a>

### ConsoleWorkspace

A workspace the caller can access, as Console reports it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The workspace ID, as Console issued it: the same value as `Workspace.id` in Business Data Repository. `node(id:)` returns the Business Data Repository `Workspace` for the workspace that your token names, and `null` for another workspace. |
| `workspaceName` | `String!` | Human-readable workspace name. |
| `organizationId` | `ID!` | The ID of the organization this workspace belongs to: the same value as `ConsoleOrganization.id`. |
| `organizationDisplayName` | `String!` | Human-readable name of the organization this workspace belongs to. |
| `status` | `String!` | Console's own workspace-status vocabulary (e.g. \"active\", \"suspended\"). |
| `zoneDisplayName` | `String!` | Human-readable name of the zone (region/cluster) this workspace is hosted in. |
| `zoneRegion` | `String` | Console's own region code for the zone this workspace is hosted in. Null when the zone has none. |

---

## Pagination types

<a id="consoleworkspaceconnection"></a>

### ConsoleWorkspaceConnection

A page of workspaces the caller can access.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ConsoleWorkspaceEdge](#consoleworkspaceedge)!]! |  |
| `nodes` | [[ConsoleWorkspace](#consoleworkspace)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter, with precision APPROXIMATE: Console counts every workspace, including any whose ID isn't a workspace ID of this deployment and which this query leaves out of `edges` and `nodes`, so the count can exceed what the connection returns. |

---

<a id="consoleworkspaceedge"></a>

### ConsoleWorkspaceEdge

An edge in a ConsoleWorkspaceConnection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [ConsoleWorkspace](#consoleworkspace)! |  |

---

## See also

* [Pagination](../../pagination.md)
* [Error handling](../../error-handling.md)
* [Limits](../../limits.md)
