---
description: >-
Reconstruct who changed an entity, what they changed, and when with
entityHistory and auditEvents queries.
---

# Tracking changes with audit

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Navixy Repository API records every create, update, and delete request on the entities it manages. You read that record through two queries. This scenario walks you through a compliance investigation: an asset's configuration changed unexpectedly, and you need to establish who changed it, what the old and new values were, and what else that actor touched in the same session.

By the end, you'll be able to read one entity's full change history, decode the stored before-and-after values, widen the search across a whole workspace, and tie an audit entry back to your own application logs.

## Prerequisites

You need an authenticated session (see [Authentication](../authentication.md)) and the ID of the workspace you're investigating.

Retrieve the workspace ID through your membership:

```graphql
query GetMyWorkspace {
  bdr {
    me {
      ... on User {
        id
        memberships(first: 10) {
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

Response:

```json
{
  "data": {
    "bdr": {
      "me": {
        "id": "019d48ea-0752-8000-801f-4445564372a1",
        "memberships": {
          "nodes": [
            {
              "workspace": {
                "id": "019d48ea-0752-8000-801f-444556437ab1",
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

You also need an audited entity that has been changed at least once. The first step below shows how to find its ID, including when the entity was already deleted. See [Which entities are audited](#which-entities-are-audited) for the list of audited entities.

## How audit works

The API writes every recorded change as an audit event, and events never change after they're written. This section covers what one event holds, which entities produce events, and how to read the recorded values.

### What one event records

An audit event describes the actor, the affected entity, the type of change, the before and after values, and the details of the request, so you can match the change against your own logs.

| Field | Used for |
| --- | --- |
| `actor` | Who made the change: a `User`, an `Integration`, or a `SystemActor`. |
| `aggregateType` / `aggregateId` | Which entity changed, as a type code plus its ID. |
| `eventType` | The type of change: `CREATED`, `UPDATED`, `DELETED`, or `RESTORED`. |
| `eventData` | The changed fields with their old and new values, plus full copies of the entity before and after the change. |
| `occurredAt` | When the change happened. It comes from the event ID, so ordering by time always matches ordering by ID. |
| `traceId` | The 32-character tracing ID of the request, for matching audit events with application logs. |
| `ipAddress` / `userAgent` | Where the request came from. |
| `workspace` | The workspace the change belongs to. Null for system-level events. |

### Which entities are audited

Eleven entity types are recorded today. `aggregateType` contains one of these codes:

| Code | Recorded event types |
| --- | --- |
| `asset` | `CREATED`, `UPDATED`, `DELETED` |
| `asset_group` | `CREATED`, `UPDATED`, `DELETED` |
| `catalog_item` | `CREATED`, `UPDATED`, `DELETED`, `RESTORED` |
| `custom_field_definition` | `CREATED`, `UPDATED`, `DELETED`, `RESTORED` |
| `device` | `CREATED`, `UPDATED`, `DELETED` |
| `geo_object` | `CREATED`, `UPDATED`, `DELETED` |
| `integration` | `CREATED`, `UPDATED`, `DELETED` |
| `inventory` | `CREATED`, `UPDATED`, `DELETED` |
| `member` | `CREATED`, `UPDATED`, `DELETED` |
| `schedule` | `CREATED`, `UPDATED`, `DELETED` |
| `workspace` | `CREATED`, `UPDATED`, `DELETED` |

Archiving a custom field definition is recorded as `UPDATED`, because archiving doesn't delete the definition. It only changes the definition's `isArchived` field, like any other update.

{% hint style="warning" %}
Some values in the [AuditEventType](../audit.md#auditeventtype) and [SourceType](../audit.md#sourcetype) enums aren't in use yet. The API accepts them in a filter, but nothing ever matches them, so you get an empty list back instead of an error. Don't build alerts on the values below until they're supported:

- Authentication events (`LOGIN`, `LOGOUT`, `FAILED_LOGIN`, `PASSWORD_RESET`, `SESSION_EXPIRED`) aren't recorded.
- Relationship events (`LINKED`, `UNLINKED`, `ATTACHED`, `DETACHED`, `ROLE_ASSIGNED`, `ROLE_REVOKED`, `PERMISSION_GRANTED`, `PERMISSION_REVOKED`) aren't recorded. Role and permission changes therefore leave no audit entry.
- User accounts aren't audited. Only the `member` record that joins a user to a workspace is.
- `sourceType` is always `API`. Filtering by `WEB`, `MOBILE`, `INTERNAL`, or `INTEGRATION` matches nothing.
{% endhint %}

### What eventData contains

`eventData` answers the investigator's main question: what exactly changed. The other event fields say who changed which entity and when, while `eventData` holds the values themselves, so you can see the old and the new state without any other source. It's a [JSON](../common.md#json) scalar with two keys:

```json
{
  "changedFields": {
    "title": { "old": "Truck B-44", "new": "Truck B-44 (retired)" }
  },
  "additionalContext": {
    "operation": "UPDATE",
    "before_state": {
      "id": "019a6b2f-793e-807b-8001-555345529b44",
      "title": "Truck B-44",
      "version": 2
    },
    "after_state": {
      "id": "019a6b2f-793e-807b-8001-555345529b44",
      "title": "Truck B-44 (retired)",
      "version": 3
    }
  }
}
```

`changedFields` holds one entry per field whose value differs, each with its `old` and `new` value. It's absent when nothing actually changed.

`additionalContext` holds the wider context. `operation` names what happened (`CREATE`, `UPDATE`, `DELETE`, or `RESTORE`), while `before_state` and `after_state` are copies of every field the entity had immediately before and immediately after the change. Where `changedFields` shows only what moved, these two show the whole record.

Which of the two you get depends on the operation, because a newly created entity has no "before" and a deleted one has no "after":

| Operation | `before_state` | `after_state` | `changedFields` |
| --- | --- | --- | --- |
| `CREATE` | absent | full copy | every field, with `"old": null` |
| `UPDATE` | full copy | full copy | only the fields that differ |
| `DELETE` | full copy | absent | every field, with `"new": null` |

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
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
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
            "id": "019a6b2f-793e-807b-8001-555345529b44",
            "title": "Truck B-44 (retired)"
          }
        ]
      }
    }
  }
}
```

The same pattern works for the other audited entity types: narrow down [devices](../devices/README.md#devices) by `identifierContains` (a fragment of an IMEI or serial number) or `titleContains` and use `titleContains` for [geo objects](../geo-objects.md#geoobjects), [schedules](../schedules.md#schedules), and [asset groups](../assets/groups.md#assetgroups).

If the entity was deleted, list queries no longer return it, but the audit trail still knows it. Search the deletions of its type and take `aggregateId` from the matching event:

```graphql
query FindDeletedAsset {
  bdr {
    auditEvents(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
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
            "aggregateId": "019a5a10-2b3c-8000-8001-77889900aabb",
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

Start with [entityHistory](../audit.md#entityhistory), which returns every audit event for one entity, newest first:

```graphql
query TruckHistory {
  bdr {
    entityHistory(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
      entityId: "019a6b2f-793e-807b-8001-555345529b44"
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
            "id": "019a6c11-0a2b-8000-8001-1122334455aa",
            "eventType": "UPDATED",
            "occurredAt": "2026-08-19T14:22:07.412Z",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736",
            "actor": {
              "id": "019d48ea-0752-8000-801f-4445564372a1",
              "title": "Anna Weber"
            }
          },
          {
            "id": "019a6b90-51c4-8000-8001-99887766aabb",
            "eventType": "UPDATED",
            "occurredAt": "2026-08-12T09:04:55.180Z",
            "actor": {
              "id": "019d48ea-0752-8000-801f-4445564372a1",
              "title": "Anna Weber"
            },
            "traceId": "1a2b3c4d5e6f70718293a4b5c6d7e8f9"
          },
          {
            "id": "019a6b2f-7940-8000-8001-556677889900",
            "eventType": "CREATED",
            "occurredAt": "2026-08-01T11:47:32.005Z",
            "actor": {
              "id": "019d48ea-0752-8000-801f-4445564372b7",
              "title": "Fleet Importer"
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

`actor` is the [Actor](../actors/README.md#actor) interface. Select `id` and `title` directly, or use an inline fragment such as `... on User { email }` when you need type-specific fields.
{% endstep %}

{% step %}

### Identify the changes

Add `eventData` to see what changed, field by field. Narrow down the response to the single event you're looking for with the `aggregateIds` and `eventTypes` filters:

```graphql
query TruckChangeDetail {
  bdr {
    entityHistory(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
      entityId: "019a6b2f-793e-807b-8001-555345529b44"
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
                "version": { "old": 2, "new": 3 }
              },
              "additionalContext": {
                "operation": "UPDATE",
                "before_state": {
                  "id": "019a6b2f-793e-807b-8001-555345529b44",
                  "title": "Truck B-44",
                  "version": 2
                },
                "after_state": {
                  "id": "019a6b2f-793e-807b-8001-555345529b44",
                  "title": "Truck B-44 (retired)",
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

`changedFields` answers the question directly: Anna Weber renamed the truck. The `version` entry appears alongside it because [optimistic locking](../optimistic-locking.md) increments the version on every successful update, so it changes on every `UPDATED` event.
{% endstep %}

{% step %}

### Widen the search to the entire workspace

You now know who made the change. To find out what else that actor did around the same time, switch to [auditEvents](../audit.md#auditevents), which covers the whole workspace rather than one entity, and narrow it down by actor and period:

```graphql
query ActorActivity {
  bdr {
    auditEvents(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
      filter: {
        actorIds: ["019d48ea-0752-8000-801f-4445564372a1"]
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
            "aggregateId": "019a6b2f-793e-807b-8001-555345529b44",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736"
          },
          {
            "occurredAt": "2026-08-19T14:22:09.988Z",
            "eventType": "DELETED",
            "aggregateType": "device",
            "aggregateId": "019a6d55-1b3c-8000-8001-aabbccddeeff",
            "traceId": "4bf92f3577b34da6a3ce929d0e0e4736"
          },
          {
            "occurredAt": "2026-08-19T14:31:44.301Z",
            "eventType": "CREATED",
            "aggregateType": "schedule",
            "aggregateId": "019a6d61-8f0a-8000-8001-0011223344ff",
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

`traceId` is the [W3C trace context](https://www.w3.org/TR/trace-context/) trace ID (32 hexadecimal characters) carried by the incoming request. One API request can change several entities, and every event it produces has the same `traceId`. Filtering by it returns that whole set:

```graphql
query OneRequestFootprint {
  bdr {
    auditEvents(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
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

The same ID appears in your own logs and tracing tools, so it's the shared value that connects the audit trail to them. Use it in the other direction too: given a suspicious request in your logs, this query tells you exactly which entities it modified.
{% endstep %}
{% endstepper %}

## Audit-related operations

### Filtering

[AuditEventFilter](../audit.md#auditeventfilter) applies to both queries. Values within one field are combined with OR, and separate fields are combined with AND. Empty arrays are ignored.

| Field | Type | Use for |
| --- | --- | --- |
| `actorIds` | `[ID!]` | Restricting to specific users or integrations |
| `aggregateTypes` | `[Code!]` | Restricting to entity types, such as `["asset", "device"]` |
| `aggregateIds` | `[ID!]` | Restricting to specific entity IDs |
| `eventTypes` | `[AuditEventType!]` | Restricting to `CREATED`, `UPDATED`, `DELETED`, or `RESTORED` |
| `sourceTypes` | `[SourceType!]` | Currently matches only `API` |
| `traceId` | `String` | Grouping every change made by one request |
| `from` / `to` | `DateTime` | Limiting the period |

For example, to see every deletion of an asset or a device in one day, run this query:

```graphql
query DeletionsToday {
  bdr {
    auditEvents(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
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

### Ordering and pagination

`OCCURRED_AT` is the only field you can order by, in either direction. Both queries return an [AuditEventConnection](../audit.md#auditeventconnection) and follow the standard cursor pagination described in [Pagination](../pagination.md):

```graphql
query AuditPage {
  bdr {
    auditEvents(
      workspaceId: "019d48ea-0752-8000-801f-444556437ab1"
      first: 50
      after: "YXVkaXQ6MjAyNi0wOC0xOVQxNDoyMjowNy40MTJa"
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

You can also fetch a single audit event through the `node` query, since `AuditEvent` implements [Node](../common.md#node):

```graphql
query OneAuditEvent {
  node(id: "019a6c11-0a2b-8000-8001-1122334455aa") {
    ... on AuditEvent {
      eventType
      occurredAt
      eventData
    }
  }
}
```

## Error handling

Audit queries are read-only, so they return the standard errors documented in [Error handling](../error-handling.md).

| Error | Cause | How to resolve it |
| --- | --- | --- |
| [400 Validation error](../error-handling.md#validation-error-400) | `workspaceId` or `entityId` isn't a valid ID, or `first` and `last` were both supplied | Check the ID format, and use either `first`/`after` or `last`/`before`, not both |
| `UNAUTHORIZED` ([401](../error-handling.md#error-codes)) | Missing or expired credentials | Re-authenticate, see [Authentication](../authentication.md) |

### How to handle an empty result set

An empty `nodes` array is a normal response, not an error. Work through the likely causes in this order:

1. The event type isn't recorded yet. Check your `eventTypes` and `sourceTypes` filters against the [warning above](#which-entities-are-audited). Authentication and relationship event types never match, and `sourceTypes` only ever matches `API`.
2. The entity type isn't audited. Only the eleven types listed above produce events. A change to a user account, a role, or an asset group membership leaves no entry.
3. The period is wrong. `from` and `to` are compared against `occurredAt` in UTC. Make sure your values are in UTC too.
4. The entity belongs to another workspace. `entityHistory` only searches the workspace whose `workspaceId` you provide, so an entity from a different workspace returns nothing.

## See also

* [Audit](../audit.md): Complete reference for all audit operations and types
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
* [Pagination](../pagination.md): Page through large result sets with cursors
* [Optimistic locking](../optimistic-locking.md): Prevent concurrent updates from overwriting each other with `version`
