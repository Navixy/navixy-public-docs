---
description: >-
  Reference for what a role grants: the workspaces it reaches, and the products,
  features, and data resources it covers, with the matching input types.
tags:
  - reference
  - beta
---

# Role access

What a role grants: the workspaces it reaches, and the products, features, and data resources it covers, with the input types that set them.

## Objects

<a id="iamselection"></a>

### IamSelection

A choice among a set of items: every current and future item when `all` is true, exactly `items`
otherwise. What an item is depends on the field: a workspace id for `IamRole.workspaces`, a
feature code for `IamRoleProductScope.features`, a data-resource item id for
`IamRoleDataResourceGrant.items`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether every current and future item is selected. `items` is empty when this is true. |
| `items` | `[String!]!` | The selected items when `all` is false. |

---

<a id="iamroleitemactions"></a>

### IamRoleItemActions

The actions a role grants on one data-resource item, in place of its grant's `actions`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `itemId` | `String!` | IAM's id for the data-resource item. |
| `actions` | `[String!]!` | The actions granted on this item. |

---

<a id="iamroledataresourcegrant"></a>

### IamRoleDataResourceGrant

What a role grants on one data-resource type of a product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `resourceType` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the data-resource type. |
| `items` | [IamSelection](#iamselection)! | The items of this type the grant covers. |
| `actions` | `[String!]!` | The actions granted on every covered item, unless `itemActions` names the item. |
| `itemActions` | [[IamRoleItemActions](#iamroleitemactions)!]! | Per-item overrides: an item listed here gets these actions instead of `actions`. |

---

<a id="iamroledataresources"></a>

### IamRoleDataResources

The data resources a role grants within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether every data resource of the product is granted. `byType` is empty when this is true. |
| `byType` | [[IamRoleDataResourceGrant](#iamroledataresourcegrant)!]! | One grant per data-resource type when `all` is false, ordered by `resourceType`. |

---

<a id="iamroleproductscope"></a>

### IamRoleProductScope

What a role grants within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productCode` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the product, such as `bdr` or `iam`. |
| `features` | [IamSelection](#iamselection)! | The product's features the role grants, by feature code. |
| `dataResources` | [IamRoleDataResources](#iamroledataresources)! | The product's data resources the role grants. |

---

<a id="iamroleproductscopes"></a>

### IamRoleProductScopes

The products a role grants.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether the role grants every product. `specific` is empty when this is true. |
| `specific` | [[IamRoleProductScope](#iamroleproductscope)!]! | One scope per product when `all` is false. |

---

## Inputs

<a id="iamselectioninput"></a>

### IamSelectionInput

A selection to store: every current and future item when `all` is true, exactly `items` otherwise.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether every current and future item is selected. `items` must then be empty. |
| `items` | `[String!]!` | The selected items when `all` is false. |

---

<a id="iamroleitemactionsinput"></a>

### IamRoleItemActionsInput

The actions to grant on one data-resource item, in place of its grant's `actions`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `itemId` | `String!` | IAM's id for the data-resource item. At most once per grant. |
| `actions` | `[String!]!` | The actions to grant on this item. |

---

<a id="iamroledataresourcegrantinput"></a>

### IamRoleDataResourceGrantInput

What to grant on one data-resource type of a product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `resourceType` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the data-resource type. At most once per product. |
| `items` | [IamSelectionInput](#iamselectioninput)! | The items of this type to cover. |
| `actions` | `[String!]!` | The actions to grant on every covered item, unless `itemActions` names the item. |
| `itemActions` | [[IamRoleItemActionsInput](#iamroleitemactionsinput)!]! | Per-item overrides. |

---

<a id="iamroledataresourcesinput"></a>

### IamRoleDataResourcesInput

The data resources to grant within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether to grant every data resource of the product. `byType` must then be empty. |
| `byType` | [[IamRoleDataResourceGrantInput](#iamroledataresourcegrantinput)!]! | One grant per data-resource type when `all` is false. |

---

<a id="iamroleproductscopeinput"></a>

### IamRoleProductScopeInput

What to grant within one product.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `productCode` | [Code](../../core-api-reference/common.md#code)! | IAM's code for the product. At most once per role. |
| `features` | [IamSelectionInput](#iamselectioninput)! | The features to grant, by feature code. |
| `dataResources` | [IamRoleDataResourcesInput](#iamroledataresourcesinput)! | The data resources to grant. |

---

<a id="iamroleproductscopesinput"></a>

### IamRoleProductScopesInput

The products to grant.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `all` | `Boolean!` | Whether to grant every product. `specific` must then be empty. |
| `specific` | [[IamRoleProductScopeInput](#iamroleproductscopeinput)!]! | One scope per product when `all` is false. |

---

## See also

* [Error handling](../../error-handling.md)
* [Limits](../../limits.md)
