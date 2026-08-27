---
description: >-
  Query the audit trail to retrieve a paginated, filterable log of entity
  create, update, and delete events across your workspace.
---

# Audit

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Audit trail for tracking changes and access to system resources.

## Queries

### auditEvents

Lists audit events for a workspace.

```graphql
auditEvents(
    workspaceId: ID!
    filter: AuditEventFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: AuditEventOrder = { field: OCCURRED_AT, direction: DESC }
  ): AuditEventConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace to retrieve audit events for. |
| `filter` | `AuditEventFilter` | Filtering options for the returned audit events. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned audit events. |

**Input types:**

<details>

<summary>AuditEventFilter</summary>

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `traceId` | `String` | Filter by trace ID. |
| `from` | [DateTime](common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](common.md#datetime) | Return events that occurred before this timestamp. |

</details>

<details>

<summary>AuditEventOrder</summary>

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### entityHistory

Retrieves the change history for any entity within a workspace.

```graphql
entityHistory(
    workspaceId: ID!
    entityId: ID!
    filter: AuditEventFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: AuditEventOrder = { field: OCCURRED_AT, direction: DESC }
  ): AuditEventConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `workspaceId` | `ID!` | The workspace that owns the entity. |
| `entityId` | `ID!` | The ID of the entity to retrieve history for. |
| `filter` | `AuditEventFilter` | Filtering options for the returned audit events. |
| `first` | `Int` | The first `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `after` | `String` | The elements that come after the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `last` | `Int` | The last `n` elements from the [paginated list](https://navixy.com/docs/navixy-repository-api/pagination). |
| `before` | `String` | The elements that come before the specified [cursor](https://navixy.com/docs/navixy-repository-api/pagination). |
| `orderBy` | `AuditEventOrder` | The ordering options for the returned audit events. |

**Input types:**

<details>

<summary>AuditEventFilter</summary>

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `traceId` | `String` | Filter by trace ID. |
| `from` | [DateTime](common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](common.md#datetime) | Return events that occurred before this timestamp. |

</details>

<details>

<summary>AuditEventOrder</summary>

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>AuditEventConnection</summary>

A paginated list of AuditEvent items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

</details>

---

## Objects

<a id="auditevent"></a>

### AuditEvent

An audit log entry recording an event that occurred in the system.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspace` | [Workspace](workspaces/README.md#workspace) | The workspace context. Null for system events. |
| `actor` | [Actor](actors/README.md#actor) | The actor who triggered the event. |
| `ipAddress` | `String` | The client IP address. |
| `userAgent` | `String` | The client User-Agent string. |
| `sourceType` | [SourceType](#sourcetype)! | The source type of the request. |
| `traceId` | `String` | The distributed tracing ID (32 hex characters) for log correlation. |
| `aggregateType` | [Code](common.md#code) | The type of entity affected. |
| `aggregateId` | `ID` | The ID of the affected entity. |
| `eventType` | [AuditEventType](#auditeventtype)! | The type of event that occurred. |
| `eventData` | [JSON](common.md#json) | The event payload with details such as changed fields. |
| `occurredAt` | [DateTime](common.md#datetime)! | The date and time when the event occurred. |

---

## Inputs

<a id="auditeventfilter"></a>

### AuditEventFilter

Filtering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `aggregateTypes` | [[Code](common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by event types (OR within field). |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `traceId` | `String` | Filter by trace ID. |
| `from` | [DateTime](common.md#datetime) | Return events that occurred after this timestamp. |
| `to` | [DateTime](common.md#datetime) | Return events that occurred before this timestamp. |

---

<a id="auditeventorder"></a>

### AuditEventOrder

Ordering options for audit events.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [AuditEventOrderField](#auditeventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](common.md#orderdirection)! | The direction to order. |

---

## Enums

<a id="sourcetype"></a>

### SourceType

The source type identifying the origin of an API request.

| Value | Description |
| ----- | ----------- |
| `WEB` | Request originated from a web browser application. |
| `MOBILE` | Request originated from a mobile application (iOS/Android). |
| `API` | Request made directly via the API. |
| `INTERNAL` | Request generated by an internal system process. |
| `INTEGRATION` | Request made by an external integration. |

---

<a id="auditeventtype"></a>

### AuditEventType

The type of event recorded in the audit log.

| Value | Description |
| ----- | ----------- |
| `LOGIN` | A user successfully authenticated. |
| `LOGOUT` | A user ended their session. |
| `FAILED_LOGIN` | An authentication attempt failed. |
| `PASSWORD_RESET` | A password reset was initiated. |
| `SESSION_EXPIRED` | A session was terminated due to inactivity. |
| `CREATED` | A new entity was created. |
| `UPDATED` | An existing entity was modified. |
| `DELETED` | An entity was deleted. |
| `RESTORED` | A soft-deleted entity was restored. |
| `ROLE_ASSIGNED` | A role was assigned to an actor. |
| `ROLE_REVOKED` | A role was removed from an actor. |
| `PERMISSION_GRANTED` | A permission was granted to a role. |
| `PERMISSION_REVOKED` | A permission was removed from a role. |
| `LINKED` | Two entities were linked together. |
| `UNLINKED` | A link between entities was removed. |
| `ATTACHED` | An entity was added to a group. |
| `DETACHED` | An entity was removed from a group. |

---

<a id="auditeventorderfield"></a>

### AuditEventOrderField

Fields available for ordering audit events.

| Value | Description |
| ----- | ----------- |
| `OCCURRED_AT` | Order by occurrence date. |

---

## Pagination types

<a id="auditeventconnection"></a>

### AuditEventConnection

A paginated list of AuditEvent items.

**Implements:** [Connection](common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[AuditEventEdge](#auditeventedge)!]! | A list of edges. |
| `nodes` | [[AuditEvent](#auditevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](common.md#countinfo) | The total count of items matching the filter. |

---

<a id="auditeventedge"></a>

### AuditEventEdge

An edge in the AuditEvent connection.

**Implements:** [Edge](common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [AuditEvent](#auditevent)! | The audit event at the end of the edge. |

---
