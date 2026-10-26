---
description: >-
  Reference for role assignments in your organization: the memberRoles and
  roleHolders queries, the memberRoleAssign and memberRoleRevoke mutations, and
  the holder and assignment types.
tags:
  - reference
  - beta
---

# Role assignments

Who holds which role in the caller's organization: the roles a member holds, the holders of a role, and the mutations that assign and revoke a role.

## Queries

### memberRoles

The roles an actor holds in the caller's organization, in any of its workspaces. Expired
  assignments and roles since deactivated or deleted are left out. Empty for an actor holding none.

```graphql
memberRoles(
    actorId: UUID!
  ): [IamMemberRoleAssignment!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |

**Output types:**

<details>

<summary>IamMemberRoleAssignment</summary>

One role a member holds in the caller's organization.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's ID for the user or API key that holds the role, the same value as `ConsoleMember.actorId`. It's a `UUID`, so `node(id:)` can't return a record for it. |
| `role` | [IamRole](roles.md#iamrole)! | The role held. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires. Null for a permanent one. |

</details>

---

### roleHolders

Who holds a role, ordered by actor id, forward only. IAM does not know memberships, so the list
  includes every principal type and people who have since left or never joined; filter on
  `principalType` and on the organization's members as needed. Empty for a role that is inactive,
  deleted, or of another organization.

```graphql
roleHolders(
    roleId: ID!
    first: Int
    after: String
    last: Int
    before: String
  ): IamRoleHolderConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `roleId` | `ID!` | IAM's id for the role. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../pagination.md). |
| `last` | `Int` | Refused with VALIDATION_ERROR: this list pages forward only. |
| `before` | `String` | Refused with VALIDATION_ERROR: this list pages forward only. |

**Output types:**

<details>

<summary>IamRoleHolderConnection</summary>

A page of the holders of one role.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IamRoleHolderEdge](#iamroleholderedge)!]! |  |
| `nodes` | [[IamRoleHolder](#iamroleholder)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | Always null: IAM reports no total for this list. See `IamRole.holderCount` for its raw count. |

</details>

---

## Mutations

### memberRoleAssign

Assigns a role to a member. IAM refuses the assignment in these cases, and the error has `dependency: "iam"`:

* A role ID that doesn't exist, or a role that was deleted: `NOT_FOUND`.
* An actor who isn't a member of your organization: `CONFLICT` with `dependencyCode` `NOT_A_MEMBER`. IAM checks this before the role.
* The Owner role, or another system role: `CONFLICT` with `dependencyCode` `SYSTEM_ROLE`.
* A role that is inactive, belongs to another organization, or doesn't reach your workspace: `CONFLICT` with `dependencyCode` `FAILED_PRECONDITION`.
* A role that you couldn't grant yourself, or an assignment that would last longer than your own access to the role: `PERMISSION_DENIED` with `dependencyCode` `GRANT_CEILING_EXCEEDED`. A permanent assignment needs your own access to the role to be permanent.

```graphql
memberRoleAssign(
    input: IamMemberRoleAssignInput!
  ): IamMemberRoleAssignPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IamMemberRoleAssignInput!` | The input fields for the assignment. |

**Input types:**

<details>

<summary>IamMemberRoleAssignInput</summary>

A role to give a member in the caller's organization and workspace. At most one of `expiresAt` and
`ttl`; neither makes the assignment permanent. Assigning a role the actor already holds replaces
its expiry.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. IAM refuses an actor who is not a member of the organization. |
| `roleId` | `ID!` | The role to assign. It must be active and reach the caller's workspace. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires. Must be in the future. |
| `ttl` | [Duration](../../core-api-reference/common.md#duration) | How long the assignment lasts from now. Must be positive. |

</details>

**Output types:**

<details>

<summary>IamMemberRoleAssignPayload</summary>

The assignment as IAM stored it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |
| `roleId` | `ID!` | The id of the assigned role. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires, as IAM resolved `expiresAt` or `ttl`. Null for a permanent one. |

</details>

---

### memberRoleRevoke

Revokes a role from a member. IAM refuses the revocation in these cases, and the error has `dependency: "iam"`:

* The member doesn't hold the role: `NOT_FOUND`.
* The revocation would leave the organization without an Owner: `CONFLICT` with `dependencyCode` `LAST_OWNER`.
* A role that you couldn't grant yourself: `PERMISSION_DENIED` with `dependencyCode` `GRANT_CEILING_EXCEEDED`.

```graphql
memberRoleRevoke(
    input: IamMemberRoleRevokeInput!
  ): IamMemberRoleRevokePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IamMemberRoleRevokeInput!` | The input fields for the revocation. |

**Input types:**

<details>

<summary>IamMemberRoleRevokeInput</summary>

A role to take from a member in the caller's organization.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |
| `roleId` | `ID!` | The role to revoke. |

</details>

**Output types:**

<details>

<summary>IamMemberRoleRevokePayload</summary>

The result of revoking a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |
| `roleId` | `ID!` | The id of the revoked role. |

</details>

---

## Objects

<a id="iammemberroleassignment"></a>

### IamMemberRoleAssignment

One role a member holds in the caller's organization.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's ID for the user or API key that holds the role, the same value as `ConsoleMember.actorId`. It's a `UUID`, so `node(id:)` can't return a record for it. |
| `role` | [IamRole](roles.md#iamrole)! | The role held. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires. Null for a permanent one. |

---

<a id="iamroleholder"></a>

### IamRoleHolder

One holder of a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's ID for the user or API key that holds the role, the same value as `ConsoleMember.actorId`. It's a `UUID`, so `node(id:)` can't return a record for it. |
| `principalType` | [IamRolePrincipalType](#iamroleprincipaltype)! | What kind of principal the actor is. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires. Null for a permanent one. |

---

<a id="iammemberroleassignpayload"></a>

### IamMemberRoleAssignPayload

The assignment as IAM stored it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |
| `roleId` | `ID!` | The id of the assigned role. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires, as IAM resolved `expiresAt` or `ttl`. Null for a permanent one. |

---

<a id="iammemberrolerevokepayload"></a>

### IamMemberRoleRevokePayload

The result of revoking a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |
| `roleId` | `ID!` | The id of the revoked role. |

---

## Inputs

<a id="iammemberroleassigninput"></a>

### IamMemberRoleAssignInput

A role to give a member in the caller's organization and workspace. At most one of `expiresAt` and
`ttl`; neither makes the assignment permanent. Assigning a role the actor already holds replaces
its expiry.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. IAM refuses an actor who is not a member of the organization. |
| `roleId` | `ID!` | The role to assign. It must be active and reach the caller's workspace. |
| `expiresAt` | [DateTime](../../core-api-reference/common.md#datetime) | When the assignment expires. Must be in the future. |
| `ttl` | [Duration](../../core-api-reference/common.md#duration) | How long the assignment lasts from now. Must be positive. |

---

<a id="iammemberrolerevokeinput"></a>

### IamMemberRoleRevokeInput

A role to take from a member in the caller's organization.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | [UUID](../../core-api-reference/common.md#uuid)! | Console's id for the actor. |
| `roleId` | `ID!` | The role to revoke. |

---

## Enums

<a id="iamroleprincipaltype"></a>

### IamRolePrincipalType

What kind of principal holds a role, as IAM derives it from the actor id. UNKNOWN is a kind this
schema does not name yet; treat it as not a person.

| Value | Description |
| ----- | ----------- |
| `USER` | A person. |
| `API_KEY` | An API key created in Console, which an agent or a script signs in with. |
| `SYSTEM` | IAM itself, for work that no person or API key started. |
| `UNKNOWN` | A kind of principal this schema does not name yet. |

---

## Pagination types

<a id="iamroleholderconnection"></a>

### IamRoleHolderConnection

A page of the holders of one role.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IamRoleHolderEdge](#iamroleholderedge)!]! |  |
| `nodes` | [[IamRoleHolder](#iamroleholder)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | Always null: IAM reports no total for this list. See `IamRole.holderCount` for its raw count. |

---

<a id="iamroleholderedge"></a>

### IamRoleHolderEdge

An edge in an IamRoleHolderConnection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [IamRoleHolder](#iamroleholder)! |  |

---

## See also

* [Pagination](../../pagination.md)
* [Error handling](../../error-handling.md)
* [Limits](../../limits.md)
