---
description: >-
  The audit event, its filter, order, and connection, shared by the audit
  queries of every product.
tags:
  - reference
  - beta
---

# Audit

The audit event, its filter, order, and connection, shared by the audit queries of every product.

## Objects

<a id="auditevent"></a>

### AuditEvent

An audit log entry recording an event that occurred in the system.

**Implements:** [Node](common.md#node)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `workspace` | [Workspace](../business-data-repository/api-reference/workspaces.md#workspace) | The workspace the event was recorded in; null for an event of no workspace: an organization's own event, or one of neither, such as a member of staff's write to billing's catalog. `BdrQuery`'s `myAuditEvents` and `myEntityHistory` list only the caller's workspace's events, so there it is always the caller's workspace. `BillingQuery.myAuditEvents` in the scope `ORGANIZATION` lists the caller's organization's own events and the events of its workspaces, and billing's staff `auditEvents` and `entityHistory` list the events of any workspace. On `IamQuery` and `ConsoleQuery`, `myAuditEvents` and `myEntityHistory` resolve it only to the caller's own workspace, and it is null for an event of any other. |
| `actorId` | `ID` | The id of the actor who triggered the event: a user's id from Console, a member of staff's, or the system's. |
| `actor` | [Actor](common.md#actor) | The actor who triggered the event; null when there is none or Console does not know the id. |
| `ipAddress` | `String` | The client IP address. |
| `userAgent` | `String` | The client User-Agent string. |
| `agentName` | `String` | The AI client's own name for itself: the name a client gave the MCP server, or the agent name in an API key's token. A label the client chose, not who triggered the event (`actor`); null when the call named no agent. |
| `sourceType` | [SourceType](#sourcetype)! | The source type of the request. |
| `traceId` | `String` | The distributed tracing ID (32 hex characters) for log correlation. |
| `aggregateType` | [Code](common.md#code) | The type of entity affected, as a code such as `asset`, `role`, or `workspace`. The codes of each product are listed in [Audit event codes](../audit-log/event-codes.md). |
| `aggregateId` | `ID` | The ID of the affected entity. |
| `eventCode` | `String!` | What happened, as the product that recorded the event names it, such as `device_created` or `role_assigned`. Each product keeps its own codes and may add new ones, so a client shows a code it does not know as it is. |
| `operation` | [AuditEventType](#auditeventtype)! | What the event did to its entity: created, modified, deleted or restored it. |
| `eventType` | [AuditEventType](#auditeventtype)! | What the event did to its entity; the same value as `operation`. |
| `product` | [Code](common.md#code)! | The product the event belongs to, such as `bdr`. |
| `eventData` | [JSON](common.md#json) | The event payload with details such as changed fields. |
| `occurredAt` | [DateTime](common.md#datetime)! | The date and time when the event occurred. |

---

## Inputs

<a id="auditeventfilter"></a>

### AuditEventFilter

Filtering options for audit events. The server that keeps the events can bound each statement of a read by a
statement timeout, five seconds by default: a read that runs longer fails with `VALIDATION_ERROR` and no field, and a
narrower one goes through: a shorter period between `from` and `to`, exact values instead of `*Contains` text, or
fewer products. A field whose filter sets `*Contains` text that is not blank adds 50 to the request's complexity, so
one request holds only a few such reads.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `actorIds` | `[ID!]` | Filter by actors (OR within field). |
| `actorIdContains` | `String` | Partial match on actor ID (case-insensitive contains). |
| `aggregateTypes` | [[Code](common.md#code)!] | Filter by entity types (OR within field). |
| `aggregateTypeContains` | `String` | Partial match on entity type (case-insensitive contains). |
| `aggregateIds` | `[ID!]` | Filter by specific entity IDs (OR within field). |
| `aggregateIdContains` | `String` | Partial match on entity ID (case-insensitive contains). |
| `eventCodes` | `[String!]` | Filter by event codes (OR within field), each matched exactly as the product recorded it, letter case included. A code no event has matches nothing. |
| `operations` | [[AuditEventType](#auditeventtype)!] | Filter by operations (OR within field). Naming both `operations` and `eventTypes` fails with `VALIDATION_ERROR`. |
| `eventTypes` | [[AuditEventType](#auditeventtype)!] | Filter by operations (OR within field); the same as `operations`. |
| `sourceTypes` | [[SourceType](#sourcetype)!] | Filter by source types (OR within field). |
| `products` | [[Code](common.md#code)!] | Filter by products (OR within field). Each field reads one product, such as `bdr` for `BdrQuery.myAuditEvents` and `billing` for `BillingQuery.myAuditEvents`. Absent or empty: that product; any other product fails with VALIDATION_ERROR. |
| `traceId` | `String` | Filter by trace ID. |
| `traceIdContains` | `String` | Partial match on trace ID (case-insensitive contains). |
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
| `WEB` | Not recorded: every event has API, INTERNAL, AGENT or ASSISTANT. |
| `MOBILE` | Not recorded: every event has API, INTERNAL, AGENT or ASSISTANT. |
| `API` | A request from a person or an API key through the API. |
| `INTERNAL` | A change the server made on its own, such as mirroring a workspace from Console. |
| `INTEGRATION` | Not recorded: every event has API, INTERNAL, AGENT or ASSISTANT. |
| `AGENT` | Reserved for a call that an AI client makes through the MCP server. Not recorded yet: every event has `API` or `INTERNAL`. |
| `ASSISTANT` | Reserved for a call that the Navixy AI assistant makes for a person. Not recorded yet: every event has `API` or `INTERNAL`. |

---

<a id="auditeventtype"></a>

### AuditEventType

The type of event recorded in the audit log.

| Value | Description |
| ----- | ----------- |
| `CREATED` | A new entity was created. |
| `UPDATED` | An existing entity was modified. |
| `DELETED` | An entity was deleted. |
| `RESTORED` | A deleted entity was restored. Recorded only for a workspace that Navixy Console reactivates. |

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

## See also

* [Pagination](../pagination.md)
* [Filtering and sorting](../filtering-and-sorting/README.md)
* [Error handling](../error-handling.md)
* [Optimistic locking](../optimistic-locking.md)
* [Limits](../limits.md)
