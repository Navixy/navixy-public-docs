---
description: >-
  Reference for the organizations a caller can reach through Navixy Console: the
  accessibleOrganizations query and the ConsoleOrganization type with its
  connection.
tags:
  - reference
  - beta
---

# Organizations

The organizations the caller can access, as Navixy Console reports them, with the caller's own roles in each.

## Queries

### accessibleOrganizations

Organizations the caller can access.

```graphql
accessibleOrganizations(
    first: Int
    after: String
    last: Int
    before: String
  ): ConsoleOrganizationConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). Requires `before` — Console pages backward only from a cursor. |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). Requires `last`. |

**Output types:**

<details>

<summary>ConsoleOrganizationConnection</summary>

A page of organizations the caller can access.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ConsoleOrganizationEdge](#consoleorganizationedge)!]! |  |
| `nodes` | [[ConsoleOrganization](#consoleorganization)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="consoleorganization"></a>

### ConsoleOrganization

An organization the caller can access, as Console reports it.

`node(id:)` and `nodes(ids:)` resolve an organization id only among the caller's own organizations, the ones
`console.accessibleOrganizations` lists. Any other organization id resolves to null, and so does every organization
id when the token does not carry the organization and workspace a call to Console needs, or Console refuses the
caller. An organization ID issued by another Navixy deployment or region is refused with `VALIDATION_ERROR`.
When Console cannot answer (not configured on the deployment, unreachable or failing), the organization
resolves to null and an error with `extensions.code` SERVICE_UNAVAILABLE and `extensions.dependency` `console` names
its path; the other ids of the same `nodes(ids:)` call resolve as usual.

**Implements:** [Node](../../core-api-reference/common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The organization ID, as Console issued it: the same value as `Workspace.organizationId` in Business Data Repository and `IamRole.organizationId`. `node(id:)` returns the organization for this ID when it's one of your own organizations. |
| `slug` | `String!` | Console's URL-safe slug for the organization. |
| `displayName` | `String!` | Human-readable organization name. |
| `orgType` | `String!` | Console's own organization-type vocabulary (e.g. \"customer\", \"partner\"). |
| `roles` | `[String!]!` | The caller's own roles in this organization, as Console reports them. |

---

## Pagination types

<a id="consoleorganizationconnection"></a>

### ConsoleOrganizationConnection

A page of organizations the caller can access.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ConsoleOrganizationEdge](#consoleorganizationedge)!]! |  |
| `nodes` | [[ConsoleOrganization](#consoleorganization)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="consoleorganizationedge"></a>

### ConsoleOrganizationEdge

An edge in a ConsoleOrganizationConnection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [ConsoleOrganization](#consoleorganization)! |  |

---

## See also

* [Pagination](../../pagination.md)
* [Error handling](../../error-handling.md)
* [Limits](../../limits.md)
