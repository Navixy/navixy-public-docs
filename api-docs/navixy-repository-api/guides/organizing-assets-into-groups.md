---
description: >-
  Organize assets into typed, color-coded collections for fleet segmentation,
  reporting, and access control.
---

# Organizing assets into groups

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Asset groups let you organize assets into named collections: by depot, project, customer, vehicle class, or any other category that makes sense for your operations. A "Hamburg Depot" group, for example, might contain all trucks assigned to that location.

The grouping system has two layers. An **asset group type** acts as a template: it classifies groups and optionally restricts which kinds of assets can join them. An **asset group** is one named collection created from that template. You create the type once, then create as many groups of that type as you need.

The API also maintains a full membership **history**: every time an asset joins or leaves a group, the event is recorded with timestamps. This lets you look up past assignments, for example to find which depot a truck belonged to during a specific period.

If you haven't created assets yet, start with [Working with assets](working-with-assets.md).

### Prerequisites

You need your workspace's ID for all asset group operations. Use the `me` query to retrieve it:

```graphql
query GetMyWorkspace {
  bdr {
    me {
      ... on User {
        memberships {
          nodes {
            workspace {
              id
              title
            }
          }
        }
      }
    }
  }
}
```

You'll receive a response:

```json
{
  "data": {
    "bdr": {
      "me": {
        "memberships": {
          "nodes": [
            {
              "workspace": {
                "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
                "title": "TransLog GmbH"
              }
            }
          ]
        }
      }
    }
  }
}
```

Use the `id` of the workspace you want to work with for all subsequent asset group operations.

### Check available asset group types

An asset group can belong to a group type, which sets its membership rules; a group created without one has no rules. Check which types already exist in your workspace before creating a new one. Run this query:

```graphql
query ListGroupTypes {
  bdr {
    assetGroupTypes(workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7") {
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
            "id": "f3a1cc20-8b2e-4d91-a73f-1ec8d0bc4e55",
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

An asset group type is a [catalog item](../catalogs/catalog-items.md) that classifies groups and sets the membership rules. Like asset types, it combines the common catalog item fields with one field of its own, `allowedAssetTypes`:

| Field | What it holds |
| --- | --- |
| `code` | A stable machine-readable identifier ([Code](../common.md#code)). Integrations and filters use it to reference the type. |
| `title` | The display name shown in UIs. |
| `meta` | UI and lifecycle properties: `description`, `origin`, `canBeDeleted`, and `hidden`. |
| `allowedAssetTypes` | Rules stating which asset types groups of this type are meant to hold, and how many of each. |
| `workspace` | The workspace that owns the type. `null` for system types. |

{% hint style="warning" %}
`code` is immutable after creation. Choose it carefully, because it's what integrations and filters will use to reference the type.
{% endhint %}

Each rule in `allowedAssetTypes` pairs an asset type with an optional `maxItems` limit, where null means unlimited. If the list is empty, the type declares no rules.

{% hint style="warning" %}
These rules are stored and returned by the API, but not enforced yet: adding an asset that breaks them still succeeds. Don't rely on the API to police group membership for now.
{% endhint %}

Types come from one of two places, and the `meta.origin` field says which: predefined by the platform (`SYSTEM`) or created by your workspace (`WORKSPACE`). You can only create, update, and delete types with `WORKSPACE` origin, because system types are read-only.

For the full field reference, see [AssetGroupType](../assets/groups.md).

### Asset groups

An asset group is a named collection that belongs to a workspace and usually has a group type (`typeId` is optional, and a group created without it carries no membership rules). For example, a "Depot" type (created once) might have three groups: "Hamburg Depot", "Berlin Depot", and "Munich Depot." Each is a separate group, but all share the same membership constraints defined by the type.

Groups have an optional `color` for visual identification in UIs, and offer two ways to query their members: `currentAssets` returns only the assets in the group right now, while `history` returns the full membership timeline, including past members.

For the full field reference, see [AssetGroup](../assets/groups.md#assetgroup).

### Membership records

Every add and remove operation creates or closes an `AssetGroupItem` record. This object tracks when an asset joined (`attachedAt`) and when it left (`detachedAt`). A null `detachedAt` means the asset is currently in the group. The history survives removal, because removing an asset only fills in `detachedAt` instead of deleting the record (a soft delete). This lets you see who was in the group at any point in the past.

For the full field reference, see [AssetGroupItem](../assets/groups.md#assetgroupitem).

## Example scenario: Setting up a depot fleet structure

TransLog GmbH wants to organize their delivery trucks by regional depot. They'll create a "Depot" group type that only accepts delivery trucks, then create a Hamburg Depot group and assign trucks to it.

{% stepper %}
{% step %}

### Create an asset group type

Start by creating the "Depot" group type. This type is meant for delivery trucks only, with no limit on how many can join a group.

{% hint style="info" %}
`version` is optional. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation CreateDepotGroupType {
  bdr {
    assetGroupTypeCreate(
      input: {
        workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
        code: "depot"
        title: "Depot"
        order: 10
        allowedAssetTypes: [
          { assetTypeId: "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22", maxItems: null }
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
          "id": "f3a1cc20-8b2e-4d91-a73f-1ec8d0bc4e55",
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
To create a group type with no asset type restrictions, omit `allowedAssetTypes` or provide an empty array. Groups of that type will then accept any asset regardless of its type.
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
        workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
        typeId: "f3a1cc20-8b2e-4d91-a73f-1ec8d0bc4e55"
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
          "id": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
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

Add Truck B-44 to the Hamburg Depot group. [assetGroupItemsAdd](../assets/groups.md#assetgroupitemsadd) takes a list of asset IDs, so one call can add several assets at once, and it returns the updated group.

```graphql
mutation AddTruckToHamburg {
  bdr {
    assetGroupItemsAdd(
      input: {
        groupId: "a9d4f810-3c67-4b02-b891-2d47e0fa3c11"
        assetIds: ["019a6b2f-793e-807b-8001-555345529b44"]
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
          "id": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
          "title": "Hamburg Depot",
          "currentAssets": {
            "total": { "count": 1 },
            "nodes": [
              {
                "id": "019a6b2f-793e-807b-8001-555345529b44",
                "title": "Truck B-44 (Berlin–Warsaw)"
              }
            ]
          }
        }
      }
    }
  }
}
```

The truck now appears in `currentAssets`, so it's now a member of the group. To see the membership record itself, including its `attachedAt` and `detachedAt` timestamps, query the group's `history` field (see [Query membership history](#query-membership-history)).

Two behaviors to know about when adding assets:

* **Adding an asset that's already in the group** succeeds silently and changes nothing. No duplicate record is created and no error is returned, so the call is idempotent.
* **The type's membership rules aren't checked.** Adding an asset whose type isn't listed in `allowedAssetTypes`, or adding more assets than `maxItems` allows, currently succeeds. See the warning in [Asset group types](#asset-group-types).
{% endstep %}

{% step %}

### Verify membership

You can verify group membership from either side: by querying the group's `currentAssets`, or by querying the asset's `groups` field.

**From the group's perspective:**

```graphql
query GetDepotMembers {
  bdr {
    assetGroup(id: "a9d4f810-3c67-4b02-b891-2d47e0fa3c11") {
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
        "id": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
        "title": "Hamburg Depot",
        "currentAssets": {
          "nodes": [
            {
              "id": "019a6b2f-793e-807b-8001-555345529b44",
              "title": "Truck B-44 (Berlin–Warsaw)",
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
    asset(id: "019a6b2f-793e-807b-8001-555345529b44") {
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
        "id": "019a6b2f-793e-807b-8001-555345529b44",
        "title": "Truck B-44 (Berlin–Warsaw)",
        "groups": {
          "nodes": [
            {
              "id": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
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

An asset can belong to several groups at once, including several groups of the same type: nothing stops a truck from being in two depot groups, or in a depot group and a maintenance-status group at the same time. The `groups` field returns all groups the asset is currently assigned to, across all group types.
{% endstep %}

{% step %}

### Update the group

The Hamburg depot is being rebranded. Update the group's title and color. Note the `version` field: include it so the update fails if someone else changed the group first, and make sure it matches the version you last read. See [Optimistic locking](../optimistic-locking.md).

```graphql
mutation RenameDepot {
  bdr {
    assetGroupUpdate(
      input: {
        id: "a9d4f810-3c67-4b02-b891-2d47e0fa3c11"
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
          "id": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
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
        groupId: "a9d4f810-3c67-4b02-b891-2d47e0fa3c11"
        assetIds: ["019a6b2f-793e-807b-8001-555345529b44"]
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
          "id": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
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

Removing an asset that isn't currently in the group succeeds silently and changes nothing, so this call is idempotent too.
{% endstep %}

{% step %}

### Query membership history

After a period of reassignments, query the full membership history of the group to see all past and current members:

```graphql
query GetDepotHistory {
  bdr {
    assetGroup(id: "a9d4f810-3c67-4b02-b891-2d47e0fa3c11") {
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
                "id": "019a6b2f-793e-807b-8001-555345529b44",
                "title": "Truck B-44 (Berlin–Warsaw)"
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

{% step %}

### Delete the group

When a depot closes and you no longer need the group, delete it using its current `version`.

```graphql
mutation CloseDepot {
  bdr {
    assetGroupDelete(
      input: { id: "a9d4f810-3c67-4b02-b891-2d47e0fa3c11", version: 2 }
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
        "deletedId": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11"
      }
    }
  }
}
```

{% hint style="warning" %}
Deleting a group also puts its membership history out of reach. The records aren't erased, but they can only be read through the group's `history` field, and a deleted group can no longer be queried. If you need the history, fetch it before deleting. Deletion can't be reversed through the API.
{% endhint %}
{% endstep %}
{% endstepper %}

### Listing asset groups

To list all groups for a workspace:

```graphql
query ListAssetGroups {
  bdr {
    assetGroups(workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7", first: 20) {
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

#### Filtering

Use `AssetGroupFilter` to narrow results by type or title. To list only depot-type groups, filter by the group type ID:

```graphql
query ListDepotGroups {
  bdr {
    assetGroups(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      filter: {
        typeIds: ["f3a1cc20-8b2e-4d91-a73f-1ec8d0bc4e55"]
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

#### Listing available group types

To see which group types are available in your workspace before creating groups:

```graphql
query ListGroupTypes {
  bdr {
    assetGroupTypes(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
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

Like asset types, group types originate either from the platform (`SYSTEM`) or from your workspace (`WORKSPACE`), visible as `meta.origin`. You can only create, update, and delete types with `WORKSPACE` origin. System types are read-only.

For details on pagination, see [Pagination](../pagination.md).

### Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a [conflict error](../error-handling.md#version-conflict-409):

```json
{
  "errors": [
    {
      "message": "Entity has been modified by another request",
      "path": ["bdr", "assetGroupUpdate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/conflict",
        "title": "Optimistic Lock Conflict",
        "status": 409,
        "code": "CONFLICT",
        "entityType": "AssetGroup",
        "entityId": "a9d4f810-3c67-4b02-b891-2d47e0fa3c11",
        "expectedVersion": 1,
        "currentVersion": 2,
        "traceId": "0af7651916cd43dd8448eb211c80319c"
      }
    }
  ]
}
```

To resolve this: re-fetch the group to get its current `version` and state, merge your intended changes, and retry the mutation with the updated version.

For a full explanation of how versioning works, see [Optimistic locking](../optimistic-locking.md).

## See also

* [Asset groups](../assets/groups.md): Complete reference for all asset group operations and types
* [Working with assets](working-with-assets.md): Create and manage assets such as vehicles and equipment
