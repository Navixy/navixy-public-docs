---
description: >-
  Organize assets into typed, color-coded collections for fleet segmentation and
  reporting.
tags:
  - guide
  - beta
---

# Organizing assets into groups

Asset groups let you organize assets into named collections: by depot, project, customer, vehicle class, or any other category that makes sense for your operations. A "Hamburg Depot" group, for example, might contain all trucks assigned to that location.

The grouping system has two layers. An **asset group type** acts as a template: it classifies groups and can list the asset types that its groups are meant to hold. An **asset group** is one named collection created from that template. You create the type once, then create as many groups of that type as you need.

The API also maintains a full membership **history**: every time an asset joins or leaves a group, the event is recorded with timestamps. This lets you look up past assignments, for example to find which depot a truck belonged to during a specific period.

If you haven't created assets yet, start with [Creating assets and assigning devices](creating-assets-and-assigning-devices.md).

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

### Check available asset group types

An asset group can have a group type that classifies it, or no type at all. Check which types already exist in your workspace before creating a new one. Run this query:

```graphql
query ListGroupTypes {
  bdr {
    assetGroupTypes {
      nodes {
        id
        code
        title
      }
    }
  }
}
```

You'll get an array of types, if any exist:

```json
{
  "data": {
    "bdr": {
      "assetGroupTypes": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-434147540001",
            "code": "depot",
            "title": "Depot"
          }
        ]
      }
    }
  }
}
```

If no group types exist, read how to create them in the [example scenario](organizing-assets-into-groups.md#example-scenario-setting-up-a-depot-fleet-structure).

## How asset groups work

### Asset group types

An asset group type is a [catalog item](../api-reference/catalogs/catalog-items.md) that classifies groups. Like asset types, it combines the common catalog item fields with one field of its own, `allowedAssetTypes`:

| Field               | What it holds                                                                                                                                  |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `code`              | A stable machine-readable identifier ([Code](../../core-api-reference/common.md#code)). Integrations and filters use it to reference the type. |
| `title`             | The display name shown in UIs.                                                                                                                 |
| `meta`              | UI and lifecycle properties: `description`, `origin`, `canBeDeleted`, and `hidden`.                                                            |
| `allowedAssetTypes` | The asset types that groups of this type are meant to hold, and how many of each.                                                              |
| `workspace`         | The workspace that owns the type. `null` for system types.                                                                                     |

{% hint style="warning" %}
`code` is immutable after creation. Choose it carefully, because it's what integrations and filters will use to reference the type.
{% endhint %}

Each entry in `allowedAssetTypes` pairs an asset type with an optional `maxItems` value: how many assets of that type one group is meant to hold. Null means no number is set. The list can be empty.

{% hint style="warning" %}
The API stores and returns `allowedAssetTypes` and `maxItems`, but doesn't check them. Adding an asset whose type isn't in the list, or more assets than `maxItems`, succeeds. If your application needs these limits, check them in your own code before you add assets.
{% endhint %}

Types come from one of two places, and the `meta.origin` field says which: predefined by the platform (`SYSTEM`) or created by your workspace (`WORKSPACE`). You can only create, update, and delete types with `WORKSPACE` origin, because system types are read-only.

For the full field reference, see [AssetGroupType](../api-reference/assets/groups.md).

### Asset groups

An asset group is a named collection that belongs to a workspace and usually has a group type (`typeId` is optional). For example, a "Depot" type (created once) might have three groups: "Hamburg Depot", "Berlin Depot", and "Munich Depot." Each is a separate group of the same type.

Groups have an optional `color` for visual identification in UIs, and offer two ways to query their members: `currentAssets` returns only the assets in the group right now, while `history` returns the full membership timeline, including past members.

For the full field reference, see [AssetGroup](../api-reference/assets/groups.md#assetgroup).

### Membership records

Every add and remove operation creates or closes an `AssetGroupItem` record. This object tracks when an asset joined (`attachedAt`) and when it left (`detachedAt`). A null `detachedAt` means the asset is currently in the group. The history survives removal, because removing an asset only fills in `detachedAt` instead of deleting the record (a soft delete). This lets you see who was in the group at any point in the past.

For the full field reference, see [AssetGroupItem](../api-reference/assets/groups.md#assetgroupitem).

## Example scenario: Setting up a depot fleet structure

TransLog GmbH wants to organize their delivery trucks by regional depot. They'll create a "Depot" group type for delivery trucks, then create a Hamburg Depot group and assign trucks to it.

{% stepper %}
{% step %}
### Create an asset group type

Start by creating the "Depot" group type. This type is meant for delivery trucks only, with no limit on how many can join a group.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation CreateDepotGroupType {
  bdr {
    assetGroupTypeCreate(
      input: {
        code: "depot"
        title: "Depot"
        order: 10
        allowedAssetTypes: [
          { assetTypeId: "019a6b2f-793e-807b-8001-434154500002", maxItems: null }
        ]
        meta: { description: "Groups trucks by regional depot location" }
      }
    ) {
      assetGroupType {
        id
        code
        title
        allowedAssetTypes {
          assetType {
            code
            title
          }
          maxItems
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroupTypeCreate": {
        "assetGroupType": {
          "id": "019a6b2f-793e-807b-8001-434147540001",
          "code": "depot",
          "title": "Depot",
          "allowedAssetTypes": [
            {
              "assetType": {
                "code": "delivery_truck",
                "title": "Delivery Truck"
              },
              "maxItems": null
            }
          ]
        }
      }
    }
  }
}
```

Save the `id`. You'll need it to create groups of this type.

{% hint style="info" %}
To create a group type that lists no asset types, omit `allowedAssetTypes` or provide an empty array. When you update a group type with [assetGroupTypeUpdate](../api-reference/assets/groups.md#assetgrouptypeupdate), a new `allowedAssetTypes` list replaces the whole stored list. Leave the field out to keep the stored list, or send an empty array to clear it. A list that names the same asset type twice is rejected with a [duplicate error](../../error-handling.md#duplicate-409) on `input.allowedAssetTypes`.
{% endhint %}
{% endstep %}

{% step %}
### Create an asset group

Create the Hamburg Depot group using the type you just created.

```graphql
mutation CreateHamburgDepot {
  bdr {
    assetGroupCreate(
      input: {
        typeId: "019a6b2f-793e-807b-8001-434147540001"
        title: "Hamburg Depot"
        color: "#1E3A5F"
      }
    ) {
      assetGroup {
        id
        version
        title
        type {
          code
          title
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroupCreate": {
        "assetGroup": {
          "id": "019a6b2f-793e-807b-8001-414752500001",
          "version": 1,
          "title": "Hamburg Depot",
          "type": {
            "code": "depot",
            "title": "Depot"
          }
        }
      }
    }
  }
}
```

Save the group `id` and `version`.
{% endstep %}

{% step %}
### Add assets to the group

Add Truck B-44 to the Hamburg Depot group. [assetGroupItemsAdd](../api-reference/assets/groups.md#assetgroupitemsadd) takes a list of asset IDs, so one call can add several assets at once, and it returns the updated group.

```graphql
mutation AddTruckToHamburg {
  bdr {
    assetGroupItemsAdd(
      input: {
        groupId: "019a6b2f-793e-807b-8001-414752500001"
        assetIds: ["019a6b2f-793e-807b-8001-415353540001"]
      }
    ) {
      assetGroup {
        id
        title
        currentAssets(first: 10) {
          total {
            count
          }
          nodes {
            id
            title
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroupItemsAdd": {
        "assetGroup": {
          "id": "019a6b2f-793e-807b-8001-414752500001",
          "title": "Hamburg Depot",
          "currentAssets": {
            "total": { "count": 1 },
            "nodes": [
              {
                "id": "019a6b2f-793e-807b-8001-415353540001",
                "title": "Truck B-44 (Hamburg–Berlin)"
              }
            ]
          }
        }
      }
    }
  }
}
```

The truck now appears in `currentAssets`, so it's now a member of the group. To see the membership record itself, including its `attachedAt` and `detachedAt` timestamps, query the group's `history` field (see [Query membership history](organizing-assets-into-groups.md#query-membership-history)).

Note these behaviors when you add assets:

* An asset that's already in the group is skipped without an error, and the other assets in the list are added. No duplicate record is created, so the call is idempotent.
* The same asset ID twice in one list, for an asset not yet in the group, fails with a [duplicate error](../../error-handling.md#duplicate-409) reading `This asset is already a member of this group`, and no asset is added.
* An asset ID that doesn't exist, was deleted, or belongs to another workspace rejects the whole request with a [not found error](../../error-handling.md#entity-not-found-404) on `input.assetIds`, and no asset is added. The error message lists the IDs it couldn't find. An asset of another workspace and an asset that doesn't exist return the same error.
* One call takes up to 2,000 asset IDs. A longer list fails with a [validation error](../../error-handling.md#validation-error-400). The same limit applies to `assetGroupItemsRemove`.
* The group itself doesn't change. Adding or removing assets doesn't increase the group's `version`, and it isn't written to the [audit log](investigating-changes-with-audit-logs.md). The group's `history` field is the record of membership changes.
{% endstep %}

{% step %}
### Verify membership

You can verify group membership from either side: by querying the group's `currentAssets`, or by querying the asset's `groups` field.

**From the group's perspective:**

```graphql
query GetDepotMembers {
  bdr {
    assetGroup(id: "019a6b2f-793e-807b-8001-414752500001") {
      id
      title
      currentAssets(first: 20) {
        nodes {
          id
          title
          type {
            code
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroup": {
        "id": "019a6b2f-793e-807b-8001-414752500001",
        "title": "Hamburg Depot",
        "currentAssets": {
          "nodes": [
            {
              "id": "019a6b2f-793e-807b-8001-415353540001",
              "title": "Truck B-44 (Hamburg–Berlin)",
              "type": {
                "code": "delivery_truck"
              }
            }
          ]
        }
      }
    }
  }
}
```

**From the asset's perspective:**

```graphql
query GetAssetGroups {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-415353540001") {
      id
      title
      groups(first: 10) {
        nodes {
          id
          title
          type {
            code
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "asset": {
        "id": "019a6b2f-793e-807b-8001-415353540001",
        "title": "Truck B-44 (Hamburg–Berlin)",
        "groups": {
          "nodes": [
            {
              "id": "019a6b2f-793e-807b-8001-414752500001",
              "title": "Hamburg Depot",
              "type": {
                "code": "depot"
              }
            }
          ]
        }
      }
    }
  }
}
```

An asset can be in any number of groups at the same time, including several groups of the same type. For example, a truck can be in two depot groups, or in a depot group and a maintenance-status group. It's in each group at most once. The `groups` field returns all groups the asset is currently assigned to, across all group types.
{% endstep %}

{% step %}
### Update the group

The Hamburg depot is being rebranded. Update the group's title and color. Note the `version` field: include it so the update fails if someone else changed the group first, and make sure it matches the version you last read. Adding the truck didn't change the group's `version`, so it's still `1`. See [Optimistic locking](../../optimistic-locking.md).

```graphql
mutation RenameDepot {
  bdr {
    assetGroupUpdate(
      input: {
        id: "019a6b2f-793e-807b-8001-414752500001"
        version: 1
        title: "Hamburg & Kiel Depot"
        color: "#0F2D52"
      }
    ) {
      assetGroup {
        id
        version
        title
        color
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroupUpdate": {
        "assetGroup": {
          "id": "019a6b2f-793e-807b-8001-414752500001",
          "version": 2,
          "title": "Hamburg & Kiel Depot",
          "color": "#0F2D52"
        }
      }
    }
  }
}
```

The `version` increments to `2`. Use this version for any further mutations on this group.

{% hint style="info" %}
You can update a group's `title` and `color`, and replace its full membership by providing `assetIds`. Its `type` is fixed at creation and cannot be changed.
{% endhint %}
{% endstep %}

{% step %}
### Remove an asset from the group

Truck B-44 has been reassigned to the Berlin depot. Remove it from the Hamburg & Kiel group.

```graphql
mutation RemoveTruckFromHamburg {
  bdr {
    assetGroupItemsRemove(
      input: {
        groupId: "019a6b2f-793e-807b-8001-414752500001"
        assetIds: ["019a6b2f-793e-807b-8001-415353540001"]
      }
    ) {
      assetGroup {
        id
        currentAssets(first: 10) {
          total {
            count
          }
          nodes {
            id
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroupItemsRemove": {
        "assetGroup": {
          "id": "019a6b2f-793e-807b-8001-414752500001",
          "currentAssets": {
            "total": { "count": 0 },
            "nodes": []
          }
        }
      }
    }
  }
}
```

Removing the truck fills in the membership record's `detachedAt` with the removal time, and the truck disappears from `currentAssets`. The record itself stays in the group's `history`: removal marks when the membership ended, it doesn't delete anything.

Removing an asset that isn't currently in the group succeeds silently and changes nothing, so this call is idempotent too. A `groupId` that doesn't exist in your workspace fails with a [not found error](../../error-handling.md#entity-not-found-404) on `input.groupId`, for both `assetGroupItemsAdd` and `assetGroupItemsRemove`.
{% endstep %}

{% step %}
### Query membership history

After a period of reassignments, query the full membership history of the group to see all past and current members:

```graphql
query GetDepotHistory {
  bdr {
    assetGroup(id: "019a6b2f-793e-807b-8001-414752500001") {
      title
      history(first: 20, orderBy: { field: ATTACHED_AT, direction: DESC }) {
        nodes {
          asset {
            id
            title
          }
          attachedAt
          detachedAt
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroup": {
        "title": "Hamburg & Kiel Depot",
        "history": {
          "nodes": [
            {
              "asset": {
                "id": "019a6b2f-793e-807b-8001-415353540001",
                "title": "Truck B-44 (Hamburg–Berlin)"
              },
              "attachedAt": "2024-03-15T09:00:00Z",
              "detachedAt": "2024-06-01T14:30:00Z"
            }
          ]
        }
      }
    }
  }
}
```

To narrow history to only currently attached assets, use the `activeOnly` filter. This returns the same set as `currentAssets`, but with `attachedAt` timestamps included:

```graphql
history(
  filter: { activeOnly: true }
  first: 20
)
```
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have a Depot group type for delivery trucks, a Hamburg Depot group with trucks as members, one membership removed, an updated group, and the membership history that records every change.
{% endhint %}

## Listing asset groups

To list all groups for a workspace:

```graphql
query ListAssetGroups {
  bdr {
    assetGroups(first: 20) {
      nodes {
        id
        title
        color
        type {
          code
          title
        }
      }
      pageInfo {
        hasNextPage
        endCursor
      }
      total {
        count
      }
    }
  }
}
```

### Filtering

Use `AssetGroupFilter` to narrow results by type or title. To list only depot-type groups, filter by the group type ID:

```graphql
query ListDepotGroups {
  bdr {
    assetGroups(
      filter: {
        typeIds: ["019a6b2f-793e-807b-8001-434147540001"]
        titleContains: "hamburg"
      }
      first: 20
    ) {
      nodes {
        id
        title
      }
    }
  }
}
```

Multiple values in `typeIds` are combined with OR, so you can retrieve groups matching any of the specified types in a single query. The `typeIds` and `titleContains` conditions are combined with AND.

### Listing available group types

To see which group types are available in your workspace before creating groups:

```graphql
query ListGroupTypes {
  bdr {
    assetGroupTypes(
      first: 20
    ) {
      nodes {
        id
        code
        title
        allowedAssetTypes {
          assetType {
            code
            title
          }
          maxItems
        }
      }
    }
  }
}
```

Like asset types, group types originate either from the platform (`SYSTEM`) or from your workspace (`WORKSPACE`), visible as `meta.origin`. You can only create, update, and delete types with `WORKSPACE` origin. System types are read-only. Deleting a group type that a group still has is refused with [`IN_USE`](../../error-handling.md#in-use-409). A group keeps its type for life, so delete those groups first, or keep the type. A custom field value or a custom field definition that names the group type refuses the delete too.

For details on pagination, see [Pagination](../../pagination.md).

## Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a [conflict error](../../error-handling.md#version-conflict-409):

```json
{
  "errors": [
    {
      "message": "Version conflict: expected 1, found 2",
      "path": ["bdr", "assetGroupUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Concurrent Modification Conflict",
        "status": 409,
        "detail": "Version conflict: expected 1, found 2",
        "instance": "/graphql",
        "code": "CONFLICT",
        "field": "input.version",
        "expectedVersion": 1,
        "currentVersion": 2,
        "traceId": "0af7651916cd43dd8448eb211c80319c",
        "timestamp": "2025-01-15T14:30:00Z"
      }
    }
  ]
}
```

To resolve this: re-fetch the group to get its current `version` and state, merge your intended changes, and retry the mutation with the updated version.

For a full explanation of how versioning works, see [Optimistic locking](../../optimistic-locking.md).

## Deleting a group

When a depot closes and you no longer need the group, delete it using its current `version`.

```graphql
mutation CloseDepot {
  bdr {
    assetGroupDelete(
      input: { id: "019a6b2f-793e-807b-8001-414752500001", version: 2 }
    ) {
      deletedId
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assetGroupDelete": {
        "deletedId": "019a6b2f-793e-807b-8001-414752500001"
      }
    }
  }
}
```

{% hint style="warning" %}
Deleting a group puts its membership history out of reach: `history` is a field of the group, and a deleted group can no longer be queried. The records aren't erased, but the API has no way to return them. If you need the history, fetch it before deleting. Deletion can't be reversed through the API.

While an asset or a geo object names the group in a custom field, `assetGroupDelete` is refused with [`IN_USE`](../../error-handling.md#in-use-409), and `extensions.blockers` names those records with the path `customFields.<code>`. Change the value on them first, or unset it if the field isn't required.
{% endhint %}

## See also

* [Asset groups](../api-reference/assets/groups.md): Complete reference for all asset group operations and types
* [Creating assets and assigning devices](creating-assets-and-assigning-devices.md): Create and manage assets such as vehicles and equipment
