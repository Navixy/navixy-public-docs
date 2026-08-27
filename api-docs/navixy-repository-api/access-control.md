---
description: >-
  Complete reference for access control: roles, permission and scope
  assignments, and the associated types and inputs.
---

# Access control

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Role-based access control (RBAC) system for managing permissions, role assignments, and resource scoping.

## Queries

### roles

Lists roles for a workspace.

```graphql
roles(
    workspaceId: ID!
    filter: CatalogItemFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: CatalogItemOrder = { field: ORDER, direction: ASC }
  ): RoleConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve roles for. |
| `filter` | `CatalogItemFilter` | Filtering options for the returned roles. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `CatalogItemOrder` | The ordering options for the returned roles. |

**Input types:**

<details>

<summary>CatalogItemFilter</summary>

Filtering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `codes` | [[Code](common.md#code)!] | Match any of these codes. |

</details>

<details>

<summary>CatalogItemOrder</summary>

Ordering options for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [CatalogItemOrderField](catalogs/catalog-items.md#catalogitemorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>RoleConnection</summary>

A paginated list of Role items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RoleEdge](#roleedge)!]! | A list of edges. |
| `nodes` | [[Role](#role)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### actorRoles

Lists actor role assignments for a workspace.

```graphql
actorRoles(
    workspaceId: ID!
    filter: ActorRoleFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: ActorRoleOrder = { field: ASSIGNED_AT, direction: DESC }
  ): ActorRoleConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve actor roles for. |
| `filter` | `ActorRoleFilter` | Filtering options for the returned actor roles. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `ActorRoleOrder` | The ordering options for the returned actor roles. |

**Input types:**

<details>

<summary>ActorRoleFilter</summary>

Filtering options for actor roles.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `roleIds` | `[ID!]` | Filter by roles (OR within field). |
| `includeExpired` | `Boolean` | Include expired role assignments. |

</details>

<details>

<summary>ActorRoleOrder</summary>

Ordering options for actor roles.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [ActorRoleOrderField](#actorroleorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>ActorRoleConnection</summary>

A paginated list of ActorRole items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ActorRoleEdge](#actorroleedge)!]! | A list of edges. |
| `nodes` | [[ActorRole](#actorrole)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### rolePermissions

Lists role permissions for a workspace.

```graphql
rolePermissions(
    workspaceId: ID!
    filter: RolePermissionFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: RolePermissionOrder = { field: GRANTED_AT, direction: DESC }
  ): RolePermissionConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve role permissions for. |
| `filter` | `RolePermissionFilter` | Filtering options for the returned role permissions. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `RolePermissionOrder` | The ordering options for the returned role permissions. |

**Input types:**

<details>

<summary>RolePermissionFilter</summary>

Filtering options for role permissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `roleIds` | `[ID!]` | Filter by roles (OR within field). |
| `permissionScopeIds` | `[ID!]` | Filter by permission scopes (OR within field). |
| `targetEntityIds` | `[ID!]` | Filter by target entities (OR within field). |

</details>

<details>

<summary>RolePermissionOrder</summary>

Ordering options for role permissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [RolePermissionOrderField](#rolepermissionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>RolePermissionConnection</summary>

A paginated list of RolePermission items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RolePermissionEdge](#rolepermissionedge)!]! | A list of edges. |
| `nodes` | [[RolePermission](#rolepermission)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### userScopes

Lists user scope restrictions for a workspace.

```graphql
userScopes(
    workspaceId: ID!
    filter: UserScopeFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: UserScopeOrder = { field: ID, direction: ASC }
  ): UserScopeConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve user scopes for. |
| `filter` | `UserScopeFilter` | Filtering options for the returned user scopes. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `UserScopeOrder` | The ordering options for the returned user scopes. |

**Input types:**

<details>

<summary>UserScopeFilter</summary>

Filtering options for user scopes.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `permissionScopeIds` | `[ID!]` | Filter by permission scopes (OR within field). |
| `targetEntityIds` | `[ID!]` | Filter by target entities (OR within field). |

</details>

<details>

<summary>UserScopeOrder</summary>

Ordering options for user scopes.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [UserScopeOrderField](#userscopeorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>UserScopeConnection</summary>

A paginated list of UserScope items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[UserScopeEdge](#userscopeedge)!]! | A list of edges. |
| `nodes` | [[UserScope](#userscope)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Mutations

### roleAssign

Assigns a role to an actor.

```graphql
roleAssign(
    input: RoleAssignInput!
  ): ActorRolePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RoleAssignInput!` | The input fields for assigning the role. |

**Input types:**

<details>

<summary>RoleAssignInput</summary>

Input for assigning a role to an actor.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | `ID!` | The actor ID (user or integration). |
| `roleId` | `ID!` | The role ID to assign. |
| `expireDate` | [DateTime](common.md#datetime) | The expiration date. Null means the role is permanent. |

</details>

**Output types:**

<details>

<summary>ActorRolePayload</summary>

The result of a role assignment mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorRole` | [ActorRole](#actorrole)! | The created role assignment. |

</details>

<details>

<summary>ActorRole (entity)</summary>

An assignment of a role to an actor.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `actor` | [Actor](actors/README.md#actor)! | The actor receiving the role. |
| `role` | [Role](#role)! | The role being assigned. |
| `assignedAt` | [DateTime](common.md#datetime)! | The date and time when the role was assigned. |
| `assignedBy` | [Actor](actors/README.md#actor) | The actor who assigned the role. |
| `expireDate` | [DateTime](common.md#datetime) | The date and time when the role expires. Null means the role is permanent. |

</details>

---

### roleRevoke

Revokes a role from an actor.

```graphql
roleRevoke(
    input: RoleRevokeInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RoleRevokeInput!` | The input fields for revoking the role. |

**Input types:**

<details>

<summary>RoleRevokeInput</summary>

Input for revoking a role from an actor.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorRoleId` | `ID!` | The actor role assignment ID to revoke. |

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

### permissionGrant

Grants a permission to a role.

```graphql
permissionGrant(
    input: PermissionGrantInput!
  ): RolePermissionPayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `PermissionGrantInput!` | The input fields for granting the permission. |

**Input types:**

<details>

<summary>PermissionGrantInput</summary>

Input for granting a permission to a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `roleId` | `ID!` | The role ID. |
| `permissionScopeId` | `ID!` | The permission scope ID. |
| `targetEntityId` | `ID` | The specific entity ID. Null means all entities of the type. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions to allow. |

</details>

**Output types:**

<details>

<summary>RolePermissionPayload</summary>

The result of a permission grant mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `rolePermission` | [RolePermission](#rolepermission)! | The created permission. |

</details>

<details>

<summary>RolePermission (entity)</summary>

A permission granted to a role.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `role` | [Role](#role)! | The role receiving the permission. |
| `permissionScope` | [PermissionScope](#permissionscope)! | The permission scope being granted. |
| `targetEntityId` | `ID` | The specific entity ID this permission applies to. Null means all entities of the type. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions allowed by this permission. |
| `grantedAt` | [DateTime](common.md#datetime)! | The date and time when the permission was granted. |
| `grantedBy` | [Actor](actors/README.md#actor)! | The actor who granted the permission. |

</details>

---

### permissionRevoke

Revokes a permission from a role.

```graphql
permissionRevoke(
    input: PermissionRevokeInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `PermissionRevokeInput!` | The input fields for revoking the permission. |

**Input types:**

<details>

<summary>PermissionRevokeInput</summary>

Input for revoking a permission from a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `permissionId` | `ID!` | The role permission ID to revoke. |

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

### userScopeSet

Sets a user scope restriction.

```graphql
userScopeSet(
    input: UserScopeSetInput!
  ): UserScopePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `UserScopeSetInput!` | The input fields for setting the user scope. |

**Input types:**

<details>

<summary>UserScopeSetInput</summary>

Input for setting a user scope restriction.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | `ID!` | The actor ID to restrict. |
| `permissionScopeId` | `ID!` | The permission scope ID. |
| `targetEntityId` | `ID!` | The specific entity ID to allow access to. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions to allow. |

</details>

**Output types:**

<details>

<summary>UserScopePayload</summary>

The result of a user scope mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `userScope` | [UserScope](#userscope)! | The created user scope restriction. |

</details>

<details>

<summary>UserScope (entity)</summary>

A whitelist filter that restricts an actor's access to specific entities.
When present, effective permissions = role permissions ∩ user scope.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `actor` | [Actor](actors/README.md#actor)! | The actor being restricted. |
| `permissionScope` | [PermissionScope](#permissionscope)! | The permission scope being filtered. |
| `targetEntityId` | `ID!` | The specific entity the actor can access. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions allowed on this specific entity. |

</details>

---

### userScopeRemove

Removes a user scope restriction.

```graphql
userScopeRemove(
    input: UserScopeRemoveInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `UserScopeRemoveInput!` | The input fields for removing the user scope. |

**Input types:**

<details>

<summary>UserScopeRemoveInput</summary>

Input for removing a user scope restriction.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `userScopeId` | `ID!` | The user scope ID to remove. |

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

### roleCreate

Creates a new role.

```graphql
roleCreate(
    input: RoleCreateInput!
  ): RolePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RoleCreateInput!` | The input fields for creating the role. |

**Input types:**

<details>

<summary>RoleCreateInput</summary>

Input for creating a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>RolePayload</summary>

The result of a role mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `role` | [Role](#role)! | The created or updated role. |

</details>

<details>

<summary>Role (entity)</summary>

A role that can be assigned to actors to grant permissions.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `permissions` | [RolePermissionConnection](#rolepermissionconnection)! | The permissions assigned to this role. |

</details>

---

### roleUpdate

Updates a role.

```graphql
roleUpdate(
    input: RoleUpdateInput!
  ): RolePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RoleUpdateInput!` | The input fields for updating the role. |

**Input types:**

<details>

<summary>RoleUpdateInput</summary>

Input for updating a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

</details>

<details>

<summary>CatalogItemMetaInput</summary>

Display properties for catalog items.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `description` | `String` | The description. |
| `hidden` | `Boolean` | Whether the item is hidden from regular UI lists. |

</details>

**Output types:**

<details>

<summary>RolePayload</summary>

The result of a role mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `role` | [Role](#role)! | The created or updated role. |

</details>

<details>

<summary>Role (entity)</summary>

A role that can be assigned to actors to grant permissions.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `permissions` | [RolePermissionConnection](#rolepermissionconnection)! | The permissions assigned to this role. |

</details>

---

### roleDelete

Deletes a role.

```graphql
roleDelete(
    input: CatalogItemDeleteInput!
  ): DeletePayload
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `CatalogItemDeleteInput!` | The input fields for deleting the role. |

**Input types:**

<details>

<summary>CatalogItemDeleteInput</summary>

Input for deleting a catalog item.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The catalog item ID to delete. |
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

<a id="role"></a>

### Role

A role that can be assigned to actors to grant permissions.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `permissions` | [RolePermissionConnection](#rolepermissionconnection)! | The permissions assigned to this role. |

---

<a id="permissionscope"></a>

### PermissionScope

A definition of a permission scope that can be granted to roles.

**Implements:** [CatalogItem](catalogs/catalog-items.md#catalogitem), [Node](common.md#node), [Versioned](common.md#versioned), [Titled](common.md#titled)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. |
| `version` | `Int!` | The version number for optimistic locking. |
| `title` | `String!` | The human-readable display name. Can be localized. |
| `code` | [Code](common.md#code)! | A machine-readable code, unique within the catalog scope. |
| `order` | `Int!` | The display order within the same level or category. |
| `catalog` | [Catalog](catalogs/catalog-items.md#catalog)! | The catalog this item belongs to. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace that owns this item. Null for system items. |
| `meta` | [CatalogItemMeta](catalogs/catalog-items.md#catalogitemmeta)! | Metadata about this item including description, origin, and display properties. |
| `module` | [Module](catalogs/system.md#module)! | The module this permission scope belongs to. |
| `entityType` | [EntityType](catalogs/system.md#entitytype)! | The entity type this permission applies to. |

---

<a id="actorrole"></a>

### ActorRole

An assignment of a role to an actor.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `actor` | [Actor](actors/README.md#actor)! | The actor receiving the role. |
| `role` | [Role](#role)! | The role being assigned. |
| `assignedAt` | [DateTime](common.md#datetime)! | The date and time when the role was assigned. |
| `assignedBy` | [Actor](actors/README.md#actor) | The actor who assigned the role. |
| `expireDate` | [DateTime](common.md#datetime) | The date and time when the role expires. Null means the role is permanent. |

---

<a id="rolepermission"></a>

### RolePermission

A permission granted to a role.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `role` | [Role](#role)! | The role receiving the permission. |
| `permissionScope` | [PermissionScope](#permissionscope)! | The permission scope being granted. |
| `targetEntityId` | `ID` | The specific entity ID this permission applies to. Null means all entities of the type. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions allowed by this permission. |
| `grantedAt` | [DateTime](common.md#datetime)! | The date and time when the permission was granted. |
| `grantedBy` | [Actor](actors/README.md#actor)! | The actor who granted the permission. |

---

<a id="userscope"></a>

### UserScope

A whitelist filter that restricts an actor's access to specific entities.
When present, effective permissions = role permissions ∩ user scope.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `actor` | [Actor](actors/README.md#actor)! | The actor being restricted. |
| `permissionScope` | [PermissionScope](#permissionscope)! | The permission scope being filtered. |
| `targetEntityId` | `ID!` | The specific entity the actor can access. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions allowed on this specific entity. |

---

<a id="actorrolepayload"></a>

### ActorRolePayload

The result of a role assignment mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorRole` | [ActorRole](#actorrole)! | The created role assignment. |

---

<a id="rolepermissionpayload"></a>

### RolePermissionPayload

The result of a permission grant mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `rolePermission` | [RolePermission](#rolepermission)! | The created permission. |

---

<a id="userscopepayload"></a>

### UserScopePayload

The result of a user scope mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `userScope` | [UserScope](#userscope)! | The created user scope restriction. |

---

<a id="rolepayload"></a>

### RolePayload

The result of a role mutation.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `role` | [Role](#role)! | The created or updated role. |

---

## Inputs

<a id="actorrolefilter"></a>

### ActorRoleFilter

Filtering options for actor roles.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `roleIds` | `[ID!]` | Filter by roles (OR within field). |
| `includeExpired` | `Boolean` | Include expired role assignments. |

---

<a id="actorroleorder"></a>

### ActorRoleOrder

Ordering options for actor roles.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [ActorRoleOrderField](#actorroleorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

---

<a id="rolepermissionfilter"></a>

### RolePermissionFilter

Filtering options for role permissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `roleIds` | `[ID!]` | Filter by roles (OR within field). |
| `permissionScopeIds` | `[ID!]` | Filter by permission scopes (OR within field). |
| `targetEntityIds` | `[ID!]` | Filter by target entities (OR within field). |

---

<a id="rolepermissionorder"></a>

### RolePermissionOrder

Ordering options for role permissions.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [RolePermissionOrderField](#rolepermissionorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

---

<a id="userscopefilter"></a>

### UserScopeFilter

Filtering options for user scopes.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `permissionScopeIds` | `[ID!]` | Filter by permission scopes (OR within field). |
| `targetEntityIds` | `[ID!]` | Filter by target entities (OR within field). |

---

<a id="userscopeorder"></a>

### UserScopeOrder

Ordering options for user scopes.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [UserScopeOrderField](#userscopeorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

---

<a id="roleassigninput"></a>

### RoleAssignInput

Input for assigning a role to an actor.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | `ID!` | The actor ID (user or integration). |
| `roleId` | `ID!` | The role ID to assign. |
| `expireDate` | [DateTime](common.md#datetime) | The expiration date. Null means the role is permanent. |

---

<a id="rolerevokeinput"></a>

### RoleRevokeInput

Input for revoking a role from an actor.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorRoleId` | `ID!` | The actor role assignment ID to revoke. |

---

<a id="permissiongrantinput"></a>

### PermissionGrantInput

Input for granting a permission to a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `roleId` | `ID!` | The role ID. |
| `permissionScopeId` | `ID!` | The permission scope ID. |
| `targetEntityId` | `ID` | The specific entity ID. Null means all entities of the type. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions to allow. |

---

<a id="permissionrevokeinput"></a>

### PermissionRevokeInput

Input for revoking a permission from a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `permissionId` | `ID!` | The role permission ID to revoke. |

---

<a id="userscopesetinput"></a>

### UserScopeSetInput

Input for setting a user scope restriction.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorId` | `ID!` | The actor ID to restrict. |
| `permissionScopeId` | `ID!` | The permission scope ID. |
| `targetEntityId` | `ID!` | The specific entity ID to allow access to. |
| `actions` | [[ActionPermission](#actionpermission)!]! | The actions to allow. |

---

<a id="userscoperemoveinput"></a>

### UserScopeRemoveInput

Input for removing a user scope restriction.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `userScopeId` | `ID!` | The user scope ID to remove. |

---

<a id="rolecreateinput"></a>

### RoleCreateInput

Input for creating a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that will own the item. |
| `code` | [Code](common.md#code) | The machine-readable code. Auto-generated from title if omitted. |
| `title` | `String!` | The display name. |
| `order` | `Int` | The display order. Auto-calculated as last position if omitted. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

<a id="roleupdateinput"></a>

### RoleUpdateInput

Input for updating a role.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The item ID to update. |
| `version` | `Int` | The current version for optimistic locking. If omitted, auto-increments without conflict check. |
| `title` | `String` | The new display name. |
| `order` | `Int` | The new display order. |
| `meta` | [CatalogItemMetaInput](catalogs/catalog-items.md#catalogitemmetainput) | The display properties. |

---

## Enums

<a id="actionpermission"></a>

### ActionPermission

Permission actions that can be granted to actors for entity operations.

| Value | Description |
| ----- | ----------- |
| `READ` | Permission to view entities and their data. |
| `CREATE` | Permission to create new entities. |
| `UPDATE` | Permission to modify existing entities. |
| `DELETE` | Permission to delete entities. |

---

<a id="actorroleorderfield"></a>

### ActorRoleOrderField

Fields available for ordering actor roles.

| Value | Description |
| ----- | ----------- |
| `ASSIGNED_AT` | Order by assignment date. |

---

<a id="rolepermissionorderfield"></a>

### RolePermissionOrderField

Fields available for ordering role permissions.

| Value | Description |
| ----- | ----------- |
| `GRANTED_AT` | Order by grant date. |

---

<a id="userscopeorderfield"></a>

### UserScopeOrderField

Fields available for ordering user scopes.

| Value | Description |
| ----- | ----------- |
| `ID` | Order by ID. |

---

## Pagination types

<a id="roleconnection"></a>

### RoleConnection

A paginated list of Role items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RoleEdge](#roleedge)!]! | A list of edges. |
| `nodes` | [[Role](#role)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="roleedge"></a>

### RoleEdge

An edge in the Role connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [Role](#role)! | The role at the end of the edge. |

---

<a id="actorroleconnection"></a>

### ActorRoleConnection

A paginated list of ActorRole items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[ActorRoleEdge](#actorroleedge)!]! | A list of edges. |
| `nodes` | [[ActorRole](#actorrole)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="actorroleedge"></a>

### ActorRoleEdge

An edge in the ActorRole connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [ActorRole](#actorrole)! | The actor role at the end of the edge. |

---

<a id="rolepermissionconnection"></a>

### RolePermissionConnection

A paginated list of RolePermission items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RolePermissionEdge](#rolepermissionedge)!]! | A list of edges. |
| `nodes` | [[RolePermission](#rolepermission)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="rolepermissionedge"></a>

### RolePermissionEdge

An edge in the RolePermission connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [RolePermission](#rolepermission)! | The role permission at the end of the edge. |

---

<a id="userscopeconnection"></a>

### UserScopeConnection

A paginated list of UserScope items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[UserScopeEdge](#userscopeedge)!]! | A list of edges. |
| `nodes` | [[UserScope](#userscope)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="userscopeedge"></a>

### UserScopeEdge

An edge in the UserScope connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [UserScope](#userscope)! | The user scope at the end of the edge. |

---
