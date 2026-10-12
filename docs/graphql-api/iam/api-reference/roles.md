---
description: >-
  Reference for the roles of your organization: the roles, role, and
  workspaceRoles queries, the roleCreate, roleUpdate, and roleDelete mutations,
  and the IamRole type with its connection.
tags:
  - reference
  - beta
---

# Roles

The roles of the caller's organization, the roles that can be assigned in the caller's workspace, and the mutations that create, replace, and delete a role.

## Queries

### roles

The roles of the caller's organization, inactive ones included, ordered by id.

```graphql
roles(
    first: Int
    after: String
    last: Int
    before: String
  ): IamRoleConnection!
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

<summary>IamRoleConnection</summary>

A page of the roles of the caller's organization.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IamRoleEdge](#iamroleedge)!]! |  |
| `nodes` | [[IamRole](#iamrole)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The number of roles in the caller's organization, inactive ones included. |

</details>

---

### role

One role of the caller's organization. Null when IAM has no such role there, or it was deleted.

```graphql
role(
    id: ID!
  ): IamRole
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | IAM's id for the role. |

**Output types:**

<details>

<summary>IamRole</summary>

A role of the caller's organization. A role belongs to one organization and reaches no other.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role ID, as IAM issued it. `IamRole` isn't a `Node`, so `node(id:)` doesn't return a role: read it with `role` or `roles`. |
| `organizationId` | `ID!` | The organization the role belongs to: the same value as `ConsoleOrganization.id`. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Null when it has none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. An inactive role is stored but grants nothing. |
| `isSystem` | `Boolean!` | Whether IAM provisioned and protects the role: the organization's Owner. `roleUpdate` and `roleDelete` refuse such a role with CONFLICT. |
| `version` | `Int!` | The version number for optimistic locking. Pass it to `roleUpdate` and `roleDelete`. |
| `workspaces` | [IamSelection](role-access.md#iamselection)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopes](role-access.md#iamroleproductscopes)! | The products the role grants. |
| `holderCount` | [Long](../../core-api-reference/common.md#long)! | How many actors hold the role now, as IAM counts them: every principal type and every holder whose assignment has not expired, including people who have since left the organization or were invited and never joined. IAM holds assignments, not memberships. It can therefore be larger than the number of members a member list shows. 0 for an inactive role, which IAM does not count. |

</details>

---

### workspaceRoles

The active roles that can be assigned in the caller's workspace, ordered by id. A role created or
  changed moments ago can be missing for about a second.

```graphql
workspaceRoles: [IamRole!]!
```

**Output types:**

<details>

<summary>IamRole</summary>

A role of the caller's organization. A role belongs to one organization and reaches no other.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role ID, as IAM issued it. `IamRole` isn't a `Node`, so `node(id:)` doesn't return a role: read it with `role` or `roles`. |
| `organizationId` | `ID!` | The organization the role belongs to: the same value as `ConsoleOrganization.id`. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Null when it has none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. An inactive role is stored but grants nothing. |
| `isSystem` | `Boolean!` | Whether IAM provisioned and protects the role: the organization's Owner. `roleUpdate` and `roleDelete` refuse such a role with CONFLICT. |
| `version` | `Int!` | The version number for optimistic locking. Pass it to `roleUpdate` and `roleDelete`. |
| `workspaces` | [IamSelection](role-access.md#iamselection)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopes](role-access.md#iamroleproductscopes)! | The products the role grants. |
| `holderCount` | [Long](../../core-api-reference/common.md#long)! | How many actors hold the role now, as IAM counts them: every principal type and every holder whose assignment has not expired, including people who have since left the organization or were invited and never joined. IAM holds assignments, not memberships. It can therefore be larger than the number of members a member list shows. 0 for an inactive role, which IAM does not count. |

</details>

---

## Mutations

### roleCreate

Creates a role in the caller's organization.

```graphql
roleCreate(
    input: IamRoleCreateInput!
  ): IamRolePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IamRoleCreateInput!` | The input fields for the new role. |

**Input types:**

<details>

<summary>IamRoleCreateInput</summary>

A new role, homed in the caller's organization. IAM refuses a role granting more than the caller's
own roles do with PERMISSION_DENIED, `dependencyCode` GRANT_CEILING_EXCEEDED.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Omitted or null for none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. |
| `workspaces` | [IamSelectionInput](role-access.md#iamselectioninput)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopesInput](role-access.md#iamroleproductscopesinput)! | The products the role grants. |

</details>

<details>

<summary>IamSelectionInput</summary>

A selection to store: every current and future item when `all` is true, exactly `items` otherwise.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether every current and future item is selected. `items` must then be empty. |
| `items` | `[String!]!` | The selected items when `all` is false. |

</details>

<details>

<summary>IamRoleProductScopesInput</summary>

The products to grant.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether to grant every product. `specific` must then be empty. |
| `specific` | [[IamRoleProductScopeInput](role-access.md#iamroleproductscopeinput)!]! | One scope per product when `all` is false. |

</details>

<details>

<summary>IamRoleProductScopeInput</summary>

What to grant within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productCode` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the product. At most once per role. |
| `features` | [IamSelectionInput](role-access.md#iamselectioninput)! | The features to grant, by feature code. |
| `dataResources` | [IamRoleDataResourcesInput](role-access.md#iamroledataresourcesinput)! | The data resources to grant. |

</details>

<details>

<summary>IamRoleDataResourcesInput</summary>

The data resources to grant within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether to grant every data resource of the product. `byType` must then be empty. |
| `byType` | [[IamRoleDataResourceGrantInput](role-access.md#iamroledataresourcegrantinput)!]! | One grant per data-resource type when `all` is false. |

</details>

<details>

<summary>IamRoleDataResourceGrantInput</summary>

What to grant on one data-resource type of a product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `resourceType` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the data-resource type. At most once per product. |
| `items` | [IamSelectionInput](role-access.md#iamselectioninput)! | The items of this type to cover. |
| `actions` | `[String!]!` | The actions to grant on every covered item, unless `itemActions` names the item. |
| `itemActions` | [[IamRoleItemActionsInput](role-access.md#iamroleitemactionsinput)!]! | Per-item overrides. |

</details>

<details>

<summary>IamRoleItemActionsInput</summary>

The actions to grant on one data-resource item, in place of its grant's `actions`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `itemId` | `String!` | IAM's id for the data-resource item. At most once per grant. |
| `actions` | `[String!]!` | The actions to grant on this item. |

</details>

**Output types:**

<details>

<summary>IamRolePayload</summary>

The role as IAM stored it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `role` | [IamRole](#iamrole)! | The role as IAM answered the write, not read back. |

</details>

<details>

<summary>IamRole (entity)</summary>

A role of the caller's organization. A role belongs to one organization and reaches no other.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role ID, as IAM issued it. `IamRole` isn't a `Node`, so `node(id:)` doesn't return a role: read it with `role` or `roles`. |
| `organizationId` | `ID!` | The organization the role belongs to: the same value as `ConsoleOrganization.id`. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Null when it has none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. An inactive role is stored but grants nothing. |
| `isSystem` | `Boolean!` | Whether IAM provisioned and protects the role: the organization's Owner. `roleUpdate` and `roleDelete` refuse such a role with CONFLICT. |
| `version` | `Int!` | The version number for optimistic locking. Pass it to `roleUpdate` and `roleDelete`. |
| `workspaces` | [IamSelection](role-access.md#iamselection)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopes](role-access.md#iamroleproductscopes)! | The products the role grants. |
| `holderCount` | [Long](../../core-api-reference/common.md#long)! | How many actors hold the role now, as IAM counts them: every principal type and every holder whose assignment has not expired, including people who have since left the organization or were invited and never joined. IAM holds assignments, not memberships. It can therefore be larger than the number of members a member list shows. 0 for an inactive role, which IAM does not count. |

</details>

---

### roleUpdate

Replaces a role's whole document: every field of the input replaces the stored one. See `IamRoleUpdateInput`.

```graphql
roleUpdate(
    input: IamRoleUpdateInput!
  ): IamRolePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IamRoleUpdateInput!` | The input fields for the role's new document. |

**Input types:**

<details>

<summary>IamRoleUpdateInput</summary>

The whole new document of a role: every field replaces the stored one, so an omitted description
removes it and an empty list empties it. There is no partial update. Refused with CONFLICT when
`version` is not the stored one, or when the role is a system role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role to replace. |
| `version` | `Int!` | The role's version as last read. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Omitted or null removes it. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. |
| `workspaces` | [IamSelectionInput](role-access.md#iamselectioninput)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopesInput](role-access.md#iamroleproductscopesinput)! | The products the role grants. |

</details>

<details>

<summary>IamSelectionInput</summary>

A selection to store: every current and future item when `all` is true, exactly `items` otherwise.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether every current and future item is selected. `items` must then be empty. |
| `items` | `[String!]!` | The selected items when `all` is false. |

</details>

<details>

<summary>IamRoleProductScopesInput</summary>

The products to grant.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether to grant every product. `specific` must then be empty. |
| `specific` | [[IamRoleProductScopeInput](role-access.md#iamroleproductscopeinput)!]! | One scope per product when `all` is false. |

</details>

<details>

<summary>IamRoleProductScopeInput</summary>

What to grant within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productCode` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the product. At most once per role. |
| `features` | [IamSelectionInput](role-access.md#iamselectioninput)! | The features to grant, by feature code. |
| `dataResources` | [IamRoleDataResourcesInput](role-access.md#iamroledataresourcesinput)! | The data resources to grant. |

</details>

<details>

<summary>IamRoleDataResourcesInput</summary>

The data resources to grant within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether to grant every data resource of the product. `byType` must then be empty. |
| `byType` | [[IamRoleDataResourceGrantInput](role-access.md#iamroledataresourcegrantinput)!]! | One grant per data-resource type when `all` is false. |

</details>

<details>

<summary>IamRoleDataResourceGrantInput</summary>

What to grant on one data-resource type of a product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `resourceType` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the data-resource type. At most once per product. |
| `items` | [IamSelectionInput](role-access.md#iamselectioninput)! | The items of this type to cover. |
| `actions` | `[String!]!` | The actions to grant on every covered item, unless `itemActions` names the item. |
| `itemActions` | [[IamRoleItemActionsInput](role-access.md#iamroleitemactionsinput)!]! | Per-item overrides. |

</details>

<details>

<summary>IamRoleItemActionsInput</summary>

The actions to grant on one data-resource item, in place of its grant's `actions`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `itemId` | `String!` | IAM's id for the data-resource item. At most once per grant. |
| `actions` | `[String!]!` | The actions to grant on this item. |

</details>

**Output types:**

<details>

<summary>IamRolePayload</summary>

The role as IAM stored it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `role` | [IamRole](#iamrole)! | The role as IAM answered the write, not read back. |

</details>

<details>

<summary>IamRole (entity)</summary>

A role of the caller's organization. A role belongs to one organization and reaches no other.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role ID, as IAM issued it. `IamRole` isn't a `Node`, so `node(id:)` doesn't return a role: read it with `role` or `roles`. |
| `organizationId` | `ID!` | The organization the role belongs to: the same value as `ConsoleOrganization.id`. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Null when it has none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. An inactive role is stored but grants nothing. |
| `isSystem` | `Boolean!` | Whether IAM provisioned and protects the role: the organization's Owner. `roleUpdate` and `roleDelete` refuse such a role with CONFLICT. |
| `version` | `Int!` | The version number for optimistic locking. Pass it to `roleUpdate` and `roleDelete`. |
| `workspaces` | [IamSelection](role-access.md#iamselection)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopes](role-access.md#iamroleproductscopes)! | The products the role grants. |
| `holderCount` | [Long](../../core-api-reference/common.md#long)! | How many actors hold the role now, as IAM counts them: every principal type and every holder whose assignment has not expired, including people who have since left the organization or were invited and never joined. IAM holds assignments, not memberships. It can therefore be larger than the number of members a member list shows. 0 for an inactive role, which IAM does not count. |

</details>

---

### roleDelete

Deletes a role and every assignment of it.

```graphql
roleDelete(
    input: IamRoleDeleteInput!
  ): IamRoleDeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `IamRoleDeleteInput!` | The input fields for the role to delete. |

**Input types:**

<details>

<summary>IamRoleDeleteInput</summary>

A role to delete. Refused with CONFLICT when `version` is not the stored one, or when the role is
a system role. Deleting a role also revokes every assignment of it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role to delete. |
| `version` | `Int!` | The role's version as last read. |

</details>

**Output types:**

<details>

<summary>IamRoleDeletePayload</summary>

The result of deleting a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deletedId` | `ID!` | The id of the deleted role. |

</details>

---

## Objects

<a id="iamrole"></a>

### IamRole

A role of the caller's organization. A role belongs to one organization and reaches no other.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role ID, as IAM issued it. `IamRole` isn't a `Node`, so `node(id:)` doesn't return a role: read it with `role` or `roles`. |
| `organizationId` | `ID!` | The organization the role belongs to: the same value as `ConsoleOrganization.id`. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Null when it has none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. An inactive role is stored but grants nothing. |
| `isSystem` | `Boolean!` | Whether IAM provisioned and protects the role: the organization's Owner. `roleUpdate` and `roleDelete` refuse such a role with CONFLICT. |
| `version` | `Int!` | The version number for optimistic locking. Pass it to `roleUpdate` and `roleDelete`. |
| `workspaces` | [IamSelection](role-access.md#iamselection)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopes](role-access.md#iamroleproductscopes)! | The products the role grants. |
| `holderCount` | [Long](../../core-api-reference/common.md#long)! | How many actors hold the role now, as IAM counts them: every principal type and every holder whose assignment has not expired, including people who have since left the organization or were invited and never joined. IAM holds assignments, not memberships. It can therefore be larger than the number of members a member list shows. 0 for an inactive role, which IAM does not count. |

---

<a id="iamrolepayload"></a>

### IamRolePayload

The role as IAM stored it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `role` | [IamRole](#iamrole)! | The role as IAM answered the write, not read back. |

---

<a id="iamroledeletepayload"></a>

### IamRoleDeletePayload

The result of deleting a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `deletedId` | `ID!` | The id of the deleted role. |

---

## Inputs

<a id="iamrolecreateinput"></a>

### IamRoleCreateInput

A new role, homed in the caller's organization. IAM refuses a role granting more than the caller's
own roles do with PERMISSION_DENIED, `dependencyCode` GRANT_CEILING_EXCEEDED.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Omitted or null for none. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. |
| `workspaces` | [IamSelectionInput](role-access.md#iamselectioninput)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopesInput](role-access.md#iamroleproductscopesinput)! | The products the role grants. |

---

<a id="iamroleupdateinput"></a>

### IamRoleUpdateInput

The whole new document of a role: every field replaces the stored one, so an omitted description
removes it and an empty list empties it. There is no partial update. Refused with CONFLICT when
`version` is not the stored one, or when the role is a system role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role to replace. |
| `version` | `Int!` | The role's version as last read. |
| `title` | `String!` | The role's name. |
| `description` | `String` | The role's description. Omitted or null removes it. |
| `isActive` | `Boolean!` | Whether the role can be assigned and grants anything. |
| `workspaces` | [IamSelectionInput](role-access.md#iamselectioninput)! | The workspaces of the organization the role reaches, by workspace id. |
| `productScopes` | [IamRoleProductScopesInput](role-access.md#iamroleproductscopesinput)! | The products the role grants. |

---

<a id="iamroledeleteinput"></a>

### IamRoleDeleteInput

A role to delete. Refused with CONFLICT when `version` is not the stored one, or when the role is
a system role. Deleting a role also revokes every assignment of it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The role to delete. |
| `version` | `Int!` | The role's version as last read. |

---

## Pagination types

<a id="iamroleconnection"></a>

### IamRoleConnection

A page of the roles of the caller's organization.

**Implements:** [Connection](../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[IamRoleEdge](#iamroleedge)!]! |  |
| `nodes` | [[IamRole](#iamrole)!]! |  |
| `pageInfo` | [PageInfo](../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../core-api-reference/common.md#countinfo) | The number of roles in the caller's organization, inactive ones included. |

---

<a id="iamroleedge"></a>

### IamRoleEdge

An edge in an IamRoleConnection.

**Implements:** [Edge](../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [IamRole](#iamrole)! |  |

---

## See also

* [Pagination](../../pagination.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
