---
description: >-
  Reconstruct who changed an entity, what they changed, and when with
  entityHistory and auditEvents queries.
tags:
  - guide
  - beta
---

# Investigating changes with audit logs

Business Data Repository records creates, updates, and deletes on nine entity types, listed below. You read that record through two queries. This scenario walks you through a compliance investigation: an asset's configuration changed unexpectedly, and you need to establish who changed it, what the old and new values were, and what else that actor touched in the same session.

By the end, you'll be able to read one entity's full change history, decode the stored before-and-after values, widen the search across a whole workspace, and tie an audit entry back to your own application logs.

## Prerequisites

You need an access token for the workspace you work in. The API takes the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication.md) for how to get a token and how the API picks the workspace.

You also need an audited entity that has been changed at least once. The first step below shows how to find its ID, including when the entity was already deleted. See [Which entities are audited](investigating-changes-with-audit-logs.md#which-entities-are-audited) for the list of audited entities.

## How audit works

The API writes every recorded change as an audit event, and events never change after they're written. Business Data Repository keeps events with no time limit. This section covers what one event holds, which entities produce events, and how to read the recorded values.

### What one event records

An audit event describes the actor, the affected entity, the type of change, the before and after values, and the details of the request, so you can match the change against your own logs.

| Field                           | Used for                                                                                                      |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `actor` / `actorId` | Who made the change: a `User`, an `ApiKey`, `Staff` at Navixy, or a `SystemActor`. `actor` is `null` when Navixy Console doesn't know the ID, and `actorId` still holds it. |
| `aggregateType` / `aggregateId` | Which entity changed, as a type code plus its ID.                                                             |
| `eventType`                     | The type of change: `CREATED`, `UPDATED`, `DELETED`, or `RESTORED`.                                           |
| `product` | The product the event belongs to. Every event that Business Data Repository records has `bdr`. Billing records its own events with `billing` and serves them through [myAuditEvents](../../billing/api-reference/audit.md#myauditevents) and [myEntityHistory](../../billing/api-reference/audit.md#myentityhistory), which take the same filter. The two queries on this page return them too when you have access to the billing product. |
| `eventData`                     | The changed fields with their old and new values, plus the entity's stored state before and after the change. |
| `occurredAt`                    | When the change happened. It comes from the event ID, so ordering by time always matches ordering by ID.      |
| `traceId`                       | The W3C trace ID of the request or other work that caused the change, for matching audit events with application logs. |
| `ipAddress` / `userAgent`       | Where the request came from.                                                                                  |
| `sourceType` | The kind of source the change came from: `INTERNAL` for a change the platform makes on its own, and `API` for a change that a caller of the API makes. Events recorded before `INTERNAL` was introduced have `API`. `AGENT`, reserved for a change that an AI client makes through the MCP server, isn't recorded. |
| `workspace` | The workspace the change belongs to. Both queries return only events from your token's workspace, including changes the platform made in it on its own. To spot those, check whether `actor` is a `SystemActor`. |
### Which entities are audited

Nine entity types are recorded. `aggregateType` contains one of these codes:

| Code                      | Recorded event types                        |
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

`catalog_item` covers every catalog item, such as asset types, tags, and catalogs. Archiving a custom field definition and restoring it with the `restore` action are both recorded as `UPDATED`, because neither deletes the definition. `RESTORED` is recorded only for a workspace that Navixy Console reactivates. Older events can also have the codes `integration` and `member`, which are no longer recorded.

{% hint style="info" %}
Some changes aren't recorded in the audit log:

* Sign-ins and other authentication events.
* Adding assets to a group and removing them. The group's `history` field records membership instead.
* Device relations and device identifiers.
* Links between devices and inventories. The device's `inventoryHistory` field records them instead.
* User accounts and workspace memberships. Navixy Console manages them.
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

If the entity was deleted, list queries no longer return it, but the audit trail still knows it. Search the deletions of its type and take `aggregateId` from the matching event:

```graphql
query FindDeletedAsset {
  bdr {
    auditEvents(
      filter: { aggregateTypes: ["asset"], eventTypes: [DELETED] }
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
      "auditEvents": {
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

Start with [entityHistory](../api-reference/audit.md#entityhistory), which returns every audit event for one entity, newest first:

```graphql
query TruckHistory {
  bdr {
    entityHistory(
      entityId: "019a6b2f-793e-807b-8001-415353540001"
      first: 20
    ) {
      total {
        count
      }
      nodes {
        id
        eventType
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
      "entityHistory": {
        "total": { "count": 3 },
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-415544540001",
            "eventType": "UPDATED",
            "occurredAt": "2026-08-19T14:22:07.412Z",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736",
            "actor": {
              "id": "019a6b2f-793e-807b-8001-555345520001",
              "title": "Anna Weber"
            }
          },
          {
            "id": "019a6b2f-793e-807b-8001-415544540002",
            "eventType": "UPDATED",
            "occurredAt": "2026-08-12T09:04:55.180Z",
            "actor": {
              "id": "019a6b2f-793e-807b-8001-555345520001",
              "title": "Anna Weber"
            },
            "traceId": "1a2b3c4d5e6f70718293a4b5c6d7e8f9"
          },
          {
            "id": "019a6b2f-793e-807b-8001-415544540003",
            "eventType": "CREATED",
            "occurredAt": "2026-08-01T11:47:32.005Z",
            "actor": {
              "id": "019a6b2f-793e-807b-8001-555345520002",
              "title": "Jonas Becker"
            },
            "traceId": "aabbccddeeff00112233445566778899"
          }
        ]
      }
    }
  }
}
```

The default ordering is `{ field: OCCURRED_AT, direction: DESC }`, so the most recent change is first. That's the one to look at.

`actor` is the [Actor](../../core-api-reference/common.md#actor) interface. Select `id` and `title`. Its types have no other fields, so select `__typename` to tell a `User` from an `ApiKey`, `Staff`, or a `SystemActor`.
{% endstep %}

{% step %}
### Identify the changes

Add `eventData` to see what changed, field by field. Narrow down the response to the single event you're looking for with the `aggregateIds` and `eventTypes` filters:

```graphql
query TruckChangeDetail {
  bdr {
    entityHistory(
      entityId: "019a6b2f-793e-807b-8001-415353540001"
      filter: { eventTypes: [UPDATED] }
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
      "entityHistory": {
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

You now know who made the change. To find out what else that actor did around the same time, switch to [auditEvents](../api-reference/audit.md#auditevents), which covers the whole workspace rather than one entity, and narrow it down by actor and period:

```graphql
query ActorActivity {
  bdr {
    auditEvents(
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
        eventType
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
      "auditEvents": {
        "total": { "count": 3 },
        "nodes": [
          {
            "occurredAt": "2026-08-19T14:22:07.412Z",
            "eventType": "UPDATED",
            "aggregateType": "asset",
            "aggregateId": "019a6b2f-793e-807b-8001-415353540001",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736"
          },
          {
            "occurredAt": "2026-08-19T14:22:09.988Z",
            "eventType": "DELETED",
            "aggregateType": "device",
            "aggregateId": "019a6b2f-793e-807b-8001-444556430021",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736"
          },
          {
            "occurredAt": "2026-08-19T14:31:44.301Z",
            "eventType": "CREATED",
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

A change that the platform makes on its own has a `SystemActor` as `actor`, the source type `INTERNAL`, and the trace ID of what caused it, such as a request or a command from Navixy Console. A scheduled run, or a Console command sent without a trace ID, gets a generated trace ID.

The `title` of a `SystemActor` names the service that made the change, such as `Console Workspace Projection` for a workspace that Navixy Console created or changed. Events recorded before each service had its own actor show `System`.

One API request can change several entities, and every event it produces has the same `traceId`. Filtering by it returns that whole set:

```graphql
query OneRequestFootprint {
  bdr {
    auditEvents(
      filter: { traceId: "4bf92f3577b34da6a3ce929d0e0e4736" }
      orderBy: { field: OCCURRED_AT, direction: ASC }
      first: 100
    ) {
      total {
        count
      }
      nodes {
        eventType
        aggregateType
        aggregateId
        eventData
      }
    }
  }
}
```

When your client sends the `traceparent` header on the request, the same ID appears in your own logs and tracing tools, so it's the shared value that connects the audit trail to them. Use it in the other direction too: given a suspicious request in your logs, this query tells you exactly which entities it modified.
{% endstep %}
{% endstepper %}

{% hint style="success" %}
You now have the change history of one entity with its before and after values, the changes made across the whole workspace in the period, and the trace IDs that connect each audit event to your own application logs.
{% endhint %}

## Filtering

[AuditEventFilter](../api-reference/audit.md#auditeventfilter) applies to both queries. Values within one field are combined with OR, and separate fields are combined with AND. Empty arrays are ignored.

| Field            | Type                | Use for                                                       |
| ---------------- | ------------------- | ------------------------------------------------------------- |
| `actorIds` | `[ID!]` | Restricting to specific actors, by the `actorId` of their events |
| `aggregateTypes` | `[Code!]`           | Restricting to entity types, such as `["asset", "device"]`    |
| `aggregateIds`   | `[ID!]`             | Restricting to specific entity IDs                            |
| `eventTypes`     | `[AuditEventType!]` | Restricting to `CREATED`, `UPDATED`, `DELETED`, or `RESTORED` |
| `sourceTypes` | `[SourceType!]` | Restricting to changes made through the API (`API`) or by the platform (`INTERNAL`) |
| `products` | `[Code!]` | Restricting to products, such as `["bdr"]`. Without it, both queries read `bdr`, and `billing` when you have access to the billing product. A product that records no audit events is refused with `VALIDATION_ERROR` |
| `traceId`        | `String`            | Grouping every change made by one request                     |
| `from` / `to`    | `DateTime`          | Limiting the period                                           |

For example, to see every deletion of an asset or a device in one day, run this query:

```graphql
query DeletionsToday {
  bdr {
    auditEvents(
      filter: {
        aggregateTypes: ["asset", "device"]
        eventTypes: [DELETED]
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

## Ordering and pagination

`OCCURRED_AT` is the only field you can order by, in either direction. Both queries return an [AuditEventConnection](../api-reference/audit.md#auditeventconnection) and follow the standard cursor pagination described in [Pagination](../../pagination.md):

```graphql
query AuditPage {
  bdr {
    auditEvents(
      first: 50
      after: "T0NDVVJSRURfQVQ6REVTQzoyMDI2LTA4LTE5VDE0OjIyOjA3LjQxMlp8MDE5YTZiMmYtNzkzZS04MDdiLTgwMDEtNDE1NTQ0NTQwMDAx"
    ) {
      pageInfo {
        hasNextPage
        endCursor
      }
      nodes {
        id
        eventType
        occurredAt
      }
    }
  }
}
```

Request `total { count }` only when you need to show a total, because counting every matching event is a second database query in addition to fetching the page. To move to the next page, check `pageInfo.hasNextPage` instead. It tells you whether another page exists and costs nothing extra.

You can also fetch a single audit event through the `node` query, since `AuditEvent` implements [Node](../../core-api-reference/common.md#node):

```graphql
query OneAuditEvent {
  node(id: "019a6b2f-793e-807b-8001-415544540001") {
    ... on AuditEvent {
      eventType
      occurredAt
      eventData
    }
  }
}
```

## Error handling

Audit queries are read-only, so they return the standard errors documented in [Error handling](../../error-handling.md).

| Error                                                                | Cause                                                                                  | How to resolve it                                                                |
| -------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| [400 Validation error](../../error-handling.md#validation-error-400) | `entityId` isn't a valid ID, or `first` or `last` is out of range                       | Check the ID format, and keep `first` and `last` between 1 and 100                        |
| `UNAUTHORIZED` ([401](../../error-handling.md#error-codes))          | The token is malformed, expired, or issued by another identity service. A request with no token gets `PERMISSION_DENIED`. | Re-authenticate, see [Authentication](../../authentication.md)                   |
| [400 Validation error](../../error-handling.md#validation-error-400) | `products` names a product that records no audit events, such as `tracking` | Name only `bdr` or `billing` |

### How to handle an empty result set

An empty `nodes` array is a normal response, not an error. Work through the likely causes in this order:

1. The filter matches no recorded value. Events have only the source types `API` and `INTERNAL`, so a `sourceTypes` filter without either of them, for example `[AGENT]`, returns nothing. `RESTORED` is recorded only for a workspace.
2. The change isn't audited. Only the nine types listed above produce events, and some changes aren't recorded at all, such as a change to a user account or an asset group membership. See [Which entities are audited](investigating-changes-with-audit-logs.md#which-entities-are-audited).
3. The period is wrong. `from` and `to` are compared against `occurredAt` in UTC. Make sure your values are in UTC too.
4. The entity belongs to another workspace. `entityHistory` only searches the workspace that your token names, so an entity from a different workspace returns nothing.

## See also

* [Audit](../api-reference/audit.md): Complete reference for all audit operations and types
* [Error handling](../../error-handling.md): Understand error structure, codes, and common error scenarios
* [Pagination](../../pagination.md): Page through large result sets with cursors
* [Optimistic locking](../../optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
