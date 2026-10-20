---
description: >-
  Reference for the members of your organization as Navixy Console reports them:
  the members query, the ConsoleMember type with what you may do to each member,
  and its connection.
tags:
  - reference
  - beta
---

# Members

The members of the caller's organization, people and agents alike, as Navixy Console reports them, with what the caller may do to them.

## Queries

### members

Members of the caller's organization, the one the token's `organization_id` claim names, ordered by actorId.

```graphql
members(
    first: Int
    after: String
    last: Int
    before: String
  ): ConsoleMemberConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../pagination.md). Requires `before`. |
| `before` | `String` | The elements that come before the specified [cursor](../../pagination.md). Requires `last`. |

**Output types:**

<details>

<summary>ConsoleMemberConnection</summary>

A page of the members of the caller's organization.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ConsoleMemberEdge](#consolememberedge)!]! |  |
| `nodes` | [[ConsoleMember](#consolemember)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="consolemember"></a>

### ConsoleMember

A person or agent in the caller's organization, as Console reports it. Reading members needs Console administration; without it the field answers
PERMISSION_DENIED.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's ID for the member, a plain UUID rather than a platform entity ID. It is the `actorId` that the `iam` role assignment operations take. |
| `principalType` | `String!` | Console's own principal vocabulary: \"human\" or \"agent\". |
| `principalSubtype` | `String` | Console's own vocabulary for the kind of agent (e.g. \"integration\", \"ai_agent\"). Null for a person. |
| `displayName` | `String` | Human-readable name. Null when Console has none. |
| `email` | `String` | Email address. Null for an agent, or when Console has none. |
| `avatarUrl` | `String` | Avatar URL. Null when Console has none. |
| `rolePreset` | `String!` | Console's own role-preset vocabulary (e.g. \"owner\"). What the organization granted, not what the caller may do. |
| `status` | `String!` | Console's own membership-status vocabulary: invited, active, suspended or removed. |
| `joinedAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the member joined the organization. Null while invited. |
| `assuranceProfileCode` | `String` | The assurance profile IAM holds for the member. Null when IAM has no access record for them, not a default profile. |
| `allowedActions` | [[ConsoleActionAffordance](products.md#consoleactionaffordance)!]! | What the caller may do to the organization's members, and why not when they can't. Console decides it once per organization, so every member carries the same list. An action the caller's role does not grant is absent from the list, rather than present with allowed: false. |

---

## Pagination types

<a id="consolememberconnection"></a>

### ConsoleMemberConnection

A page of the members of the caller's organization.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ConsoleMemberEdge](#consolememberedge)!]! |  |
| `nodes` | [[ConsoleMember](#consolemember)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="consolememberedge"></a>

### ConsoleMemberEdge

An edge in a ConsoleMemberConnection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [ConsoleMember](#consolemember)! |  |

---

## See also

* [Pagination](../../pagination.md)
* [Error handling](../../error-handling.md)
* [Limits](../../limits.md)
