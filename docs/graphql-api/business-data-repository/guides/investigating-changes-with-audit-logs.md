---
description: >-
  Reconstruct who changed an entity, what they changed, and when with
  myEntityHistory and myAuditEvents queries.
tags:
  - guide
  - beta
---

# Investigating changes with audit logs

Business Data Repository records creates, updates, and deletes on nine entity types, listed below. You read that record through two queries. This scenario walks you through a compliance investigation: an asset's configuration changed unexpectedly, and you need to establish who changed it, what the old and new values were, and what else that actor touched in the same session.

By the end, you'll be able to read one entity's full change history, decode the stored before-and-after values, widen the search across a whole workspace, and tie an audit entry back to your own application logs.

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication/README.md) for how to get a token and how the API chooses the workspace.

You also need an audited entity that has been changed at least once. The first step below shows how to find its ID, including when the entity was already deleted. See [Which entities are audited](investigating-changes-with-audit-logs.md#which-entities-are-audited) for the list of audited entities.

## How audit works

Every product that records audit events uses the same event format, filter, and pagination. [Audit log](../../audit-log/README.md) explains them: what one event records, the event codes and operations, and how to filter and page. This section covers what is specific to Business Data Repository: which entities produce events and how to read their recorded values.

Both queries on this page, [myAuditEvents](../api-reference/audit.md#myauditevents) and [myEntityHistory](../api-reference/audit.md#myentityhistory), return only the events of the workspace your token names, with `product` set to `bdr`. They include the changes the platform made in the workspace on its own. To spot those, check whether `actor` is a `SystemActor`.

### Which entities are audited

Nine entity types are recorded. `aggregateType` contains one of these codes:

| Code                      | Recorded operations                         |
| ------------------------- | ------------------------------------------- |
| `asset`                   | `CREATED`, `UPDATED`, `DELETED`             |
| `asset_group`             | `CREATED`, `UPDATED`, `DELETED`             |
| `catalog_item`            | `CREATED`, `UPDATED`, `DELETED`             |
| `custom_field_definition` | `CREATED`, `UPDATED`, `DELETED`             |
| `device`                  | `CREATED`, `UPDATED`, `DELETED`             |
| `geo_object`              | `CREATED`, `UPDATED`, `DELETED`             |
| `inventory`               | `CREATED`, `UPDATED`, `DELETED`             |
| `schedule`                | `CREATED`, `UPDATED`, `DELETED`             |
| `workspace`               | `CREATED`, `UPDATED`, `DELETED`, `RESTORED` |

`catalog_item` covers every catalog item, such as asset types, tags, and catalogs. Archiving a custom field definition and restoring it with the `restore` action both have the operation `UPDATED`, because neither deletes the definition. Their event codes tell them apart: `custom_field_def_archived` and `custom_field_def_restored`. `RESTORED` is recorded only for a workspace that Console reactivates. Older events can also have the aggregate types `integration` and `member`, which are no longer recorded. [Audit event codes](../../audit-log/event-codes.md#business-data-repository) lists the event code of every change.

{% hint style="info" %}
Some changes aren't recorded in the audit log:

* Sign-ins and other authentication events.
* Adding assets to a group and removing them. The group's `history` field records membership instead.
* Device relations and device identifiers.
* Links between devices and inventories. The device's `inventoryHistory` field records them instead.
* User accounts and workspace memberships. Console records changes to members in its own audit log.
{% endhint %}

### What eventData contains

`eventData` holds the answer to the investigator's main question: what exactly changed. The other event fields say who changed which entity and when, while `eventData` holds the values themselves, so you can see the old and the new state without any other source. It's a [JSON](../../core-api-reference/common.md#json) scalar with two keys:

```json
{
  "changedFields": {
    "title": { "old": "Truck B-44", "new": "Truck B-44 (retired)" },
    "updatedAt": { "old": "2026-08-12T09:04:55.180Z", "new": "2026-08-19T14:22:07.412Z" },
    "version": { "old": 2, "new": 3 }
  },
  "additionalContext": {
    "operation": "UPDATE",
    "before_state": {
      "id": "019a6b2f-793e-807b-8001-415353540001",
      "title": "Truck B-44",
      "updatedAt": "2026-08-12T09:04:55.180Z",
      "version": 2
    },
    "after_state": {
      "id": "019a6b2f-793e-807b-8001-415353540001",
      "title": "Truck B-44 (retired)",
      "updatedAt": "2026-08-19T14:22:07.412Z",
      "version": 3
    }
  }
}
```

`changedFields` holds one entry per field whose value differs, each with its `old` and `new` value. Every update sets a new `updatedAt`, so on an `UPDATED` event `changedFields` always lists at least that field.

`additionalContext` holds the wider context. `operation` names what happened (`CREATE`, `UPDATE`, `DELETE`, or `RESTORE`). `before_state` and `after_state` hold the entity's stored state immediately before and immediately after the change. Where `changedFields` shows only what changed, these two show the whole record.

The keys in `changedFields` and in both states are the property names that Business Data Repository stores internally, not the GraphQL field names. For example, an asset's type is stored as `assetTypeId`, and all its custom field values are in one key, `customFieldsData`. A reference to another entity is stored as that entity's ID. The API doesn't guarantee these keys, so use them to read a change rather than in automated logic.

Which of the two you get depends on the operation, because a newly created entity has no "before" and a deleted one has no "after":

| Operation | `before_state` | `after_state` | `changedFields`                               |
| --------- | -------------- | ------------- | --------------------------------------------- |
| `CREATE`  | absent         | present       | every field that has a value, with `"old": null` |
| `UPDATE`  | present        | present       | only the fields that differ                   |
| `DELETE`  | present        | absent        | every field that had a value, with `"new": null` |
| `RESTORE` | present        | present       | only the fields that differ                   |

`eventData` is plain JSON rather than a typed GraphQL object, so you can't select individual keys inside it in the query. Request it as one field and read the values in your own code.

## How to track a logged change

{% stepper %}
{% step %}
### Find the ID of the entity to investigate

Both audit queries work with entity IDs, so start by finding the ID of the entity you're investigating. List queries take filters that narrow down the output by what you already know, such as a name or a hardware identifier. For example, to find a truck by a fragment of its name, run this query:

```graphql
query FindTruck {
  bdr {
    assets(
      filter: { titleContains: "B-44" }
      first: 5
    ) {
      nodes {
        id
        title
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
      "assets": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-415353540001",
            "title": "Truck B-44 (retired)"
          }
        ]
      }
    }
  }
}
```

The same pattern works for the other audited entity types: narrow down [devices](../api-reference/devices/#devices) by `identifierContains` (a fragment of an IMEI or serial number) or `titleContains` and use `titleContains` for [geo objects](../api-reference/geo-objects.md#geoobjects), [schedules](../api-reference/schedules.md#schedules), and [asset groups](../api-reference/assets/groups.md#assetgroups).

If the entity was deleted, list queries no longer return it, but the audit log still has its events. Search the deletions of its type by event code and take `aggregateId` from the matching event:

```graphql
query FindDeletedAsset {
  bdr {
    myAuditEvents(
      filter: { eventCodes: ["asset_deleted"] }
      first: 20
    ) {
      nodes {
        aggregateId
        occurredAt
        actor {
          title
        }
      }
    }
  }
}
```

The response lists one entry per deleted asset. `aggregateId` is the deleted entity's ID:

```json
{
  "data": {
    "bdr": {
      "myAuditEvents": {
        "nodes": [
          {
            "aggregateId": "019a6b2f-793e-807b-8001-415353540009",
            "occurredAt": "2026-08-14T10:05:11.204Z",
            "actor": { "title": "Anna Weber" }
          }
        ]
      }
    }
  }
}
```

Either way, the ID you found is the entity ID for the rest of the scenario.
{% endstep %}

{% step %}
### Read the entity's history

Start with [myEntityHistory](../api-reference/audit.md#myentityhistory), which returns every audit event for one entity, newest first:

```graphql
query TruckHistory {
  bdr {
    myEntityHistory(
      entityId: "019a6b2f-793e-807b-8001-415353540001"
      first: 20
    ) {
      total {
        count
      }
      nodes {
        id
        eventCode
        occurredAt
        traceId
        actor {
          id
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
      "myEntityHistory": {
        "total": { "count": 3 },
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-415544540001",
            "eventCode": "asset_updated",
            "occurredAt": "2026-08-19T14:22:07.412Z",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736",
            "actor": {
              "id": "019a6b2f-793e-807b-8001-555345520001",
              "title": "Anna Weber"
            }
          },
          {
            "id": "019a6b2f-793e-807b-8001-415544540002",
            "eventCode": "asset_updated",
            "occurredAt": "2026-08-12T09:04:55.180Z",
            "traceId": "1a2b3c4d5e6f70718293a4b5c6d7e8f9",
            "actor": {
              "id": "019a6b2f-793e-807b-8001-555345520001",
              "title": "Anna Weber"
            }
          },
          {
            "id": "019a6b2f-793e-807b-8001-415544540003",
            "eventCode": "asset_created",
            "occurredAt": "2026-08-01T11:47:32.005Z",
            "traceId": "aabbccddeeff00112233445566778899",
            "actor": {
              "id": "019a6b2f-793e-807b-8001-555345520002",
              "title": "Jonas Becker"
            }
          }
        ]
      }
    }
  }
}
```

The most recent change is first. That's the one to look at.

`actor` is the [Actor](../../core-api-reference/common.md#actor) interface. Select `id` and `title`. Its types have no other fields, so select `__typename` to tell a `User` from an `ApiKey`, `Staff`, or a `SystemActor`.
{% endstep %}

{% step %}
### Identify the changes

Add `eventData` to see what changed, field by field. Narrow down the response to the single event you're looking for with the `operations` filter:

```graphql
query TruckChangeDetail {
  bdr {
    myEntityHistory(
      entityId: "019a6b2f-793e-807b-8001-415353540001"
      filter: { operations: [UPDATED] }
      first: 1
    ) {
      nodes {
        occurredAt
        actor {
          title
        }
        ipAddress
        userAgent
        eventData
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
      "myEntityHistory": {
        "nodes": [
          {
            "occurredAt": "2026-08-19T14:22:07.412Z",
            "actor": { "title": "Anna Weber" },
            "ipAddress": "203.0.113.47",
            "userAgent": "NavixyFleetConsole/3.1 (Windows NT 10.0)",
            "eventData": {
              "changedFields": {
                "title": { "old": "Truck B-44", "new": "Truck B-44 (retired)" },
                "updatedAt": { "old": "2026-08-12T09:04:55.180Z", "new": "2026-08-19T14:22:07.412Z" },
                "version": { "old": 2, "new": 3 }
              },
              "additionalContext": {
                "operation": "UPDATE",
                "before_state": {
                  "id": "019a6b2f-793e-807b-8001-415353540001",
                  "title": "Truck B-44",
                  "updatedAt": "2026-08-12T09:04:55.180Z",
                  "version": 2
                },
                "after_state": {
                  "id": "019a6b2f-793e-807b-8001-415353540001",
                  "title": "Truck B-44 (retired)",
                  "updatedAt": "2026-08-19T14:22:07.412Z",
                  "version": 3
                }
              }
            }
          }
        ]
      }
    }
  }
}
```

`changedFields` shows the answer directly: Anna Weber renamed the truck. The `updatedAt` and `version` entries appear alongside it because every successful update of an asset sets a new `updatedAt` and increases the version that [optimistic locking](../../optimistic-locking.md) checks.
{% endstep %}

{% step %}
### Widen the search to the entire workspace

You now know who made the change. To find out what else that actor did around the same time, switch to [myAuditEvents](../api-reference/audit.md#myauditevents), which covers the whole workspace rather than one entity, and narrow it down by actor and period:

```graphql
query ActorActivity {
  bdr {
    myAuditEvents(
      filter: {
        actorIds: ["019a6b2f-793e-807b-8001-555345520001"]
        from: "2026-08-19T14:00:00.000Z"
        to: "2026-08-19T15:00:00.000Z"
      }
      orderBy: { field: OCCURRED_AT, direction: ASC }
      first: 50
    ) {
      total {
        count
      }
      nodes {
        occurredAt
        eventCode
        aggregateType
        aggregateId
        traceId
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
      "myAuditEvents": {
        "total": { "count": 3 },
        "nodes": [
          {
            "occurredAt": "2026-08-19T14:22:07.412Z",
            "eventCode": "asset_updated",
            "aggregateType": "asset",
            "aggregateId": "019a6b2f-793e-807b-8001-415353540001",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736"
          },
          {
            "occurredAt": "2026-08-19T14:22:09.988Z",
            "eventCode": "device_deleted",
            "aggregateType": "device",
            "aggregateId": "019a6b2f-793e-807b-8001-444556430021",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736"
          },
          {
            "occurredAt": "2026-08-19T14:31:44.301Z",
            "eventCode": "schedule_created",
            "aggregateType": "schedule",
            "aggregateId": "019a6b2f-793e-807b-8001-534348440001",
            "traceId": "9f8e7d6c5b4a39281706f5e4d3c2b1a0"
          }
        ]
      }
    }
  }
}
```

Switching `direction` to `ASC` puts the events in the order they happened, which is what you want when retracing a sequence of actions.

Note that the first two events share a `traceId`. That means they came from the same request: renaming the asset and deleting a device happened in one operation.
{% endstep %}

{% step %}
### Match audit events with your application logs

`traceId` is the [W3C trace context](https://www.w3.org/TR/trace-context/) trace ID from the request's `traceparent` header, when that header is present and well-formed. If the header is missing or malformed (wrong version, uppercase hex, an all-zero ID), the API generates a trace ID instead. Either way, you see the ID in the event's `traceId` and in `extensions.traceId` of any error the request raised. A generated ID has no match in your own logs, since your client never sent it.

A change that the platform makes on its own has a `SystemActor` as `actor`, the source type `INTERNAL`, and the trace ID of what caused it, such as a request or a command from Console. A scheduled run, or a Console command sent without a trace ID, gets a generated trace ID.

The `title` of a `SystemActor` names the service that made the change, such as `Console Workspace Projection` for a workspace that Console created or changed. Events recorded before each service had its own actor show `System`.

One API request can change several entities, and every event it produces has the same `traceId`. Filtering by it returns that whole set:

```graphql
query OneRequestFootprint {
  bdr {
    myAuditEvents(
      filter: { traceId: "4bf92f3577b34da6a3ce929d0e0e4736" }
      orderBy: { field: OCCURRED_AT, direction: ASC }
      first: 100
    ) {
      total {
        count
      }
      nodes {
        eventCode
        aggregateType
        aggregateId
        eventData
      }
    }
  }
}
```

When your client sends the `traceparent` header on the request, the same ID appears in your own logs and tracing tools, so it's the shared value that connects the audit log to them. Use it in the other direction too: given a suspicious request in your logs, this query tells you exactly which entities it modified.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have the change history of one entity with its before and after values, the changes made across the whole workspace in the period, and the trace IDs that connect each audit event to your own application logs.
{% endhint %}

## Filtering, ordering, and pagination

Both queries accept the filter, order, and pagination that every audit query shares, described in [Filtering, ordering, and pagination](../../audit-log/README.md#filtering-ordering-and-pagination). The `products` filter accepts only `bdr` here.

For example, to see every deletion of an asset or a device in one day, run this query:

```graphql
query DeletionsToday {
  bdr {
    myAuditEvents(
      filter: {
        eventCodes: ["asset_deleted", "device_deleted"]
        from: "2026-08-19T00:00:00.000Z"
        to: "2026-08-20T00:00:00.000Z"
      }
      first: 100
    ) {
      total {
        count
      }
      nodes {
        occurredAt
        aggregateType
        aggregateId
        actor {
          title
        }
      }
    }
  }
}
```

You can also fetch a single Business Data Repository audit event by its ID through the `node` query:

```graphql
query OneAuditEvent {
  node(id: "019a6b2f-793e-807b-8001-415544540001") {
    ... on AuditEvent {
      eventCode
      occurredAt
      eventData
    }
  }
}
```

## Error handling

Audit queries return the errors described in [Audit log errors](../../audit-log/README.md#audit-log-errors). A `products` filter that names any product other than `bdr` is refused with `VALIDATION_ERROR`.

### How to handle an empty result set

An empty `nodes` array is a normal response, not an error. Work through the likely causes in this order:

1. The filter matches no recorded value. An event code in `eventCodes` matches only when its letter case is the same as the recorded code, and a code that Business Data Repository never records matches nothing. `RESTORED` is recorded only for a workspace.
2. The change isn't audited. Only the nine types listed above produce events, and some changes aren't recorded at all, such as a change to a user account or an asset group membership. See [Which entities are audited](investigating-changes-with-audit-logs.md#which-entities-are-audited).
3. The period is wrong. `from` and `to` are compared against `occurredAt` in UTC. Make sure your values are in UTC too.
4. The entity belongs to another workspace. `myEntityHistory` only searches the workspace that your token names, so an entity from a different workspace returns nothing.

## See also

* [Audit log](../../audit-log/README.md): How the audit log works in every product, and the shared filter and errors
* [Audit event codes](../../audit-log/event-codes.md): The codes of every product with their operations
* [Audit](../api-reference/audit.md): Complete reference for the Business Data Repository audit queries
* [Pagination](../../pagination.md): Page through large result sets with cursors
* [Optimistic locking](../../optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
