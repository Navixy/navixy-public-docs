---
description: >-
  Which products of Navixy GraphQL API record who changed what and when, what
  one audit event contains, and how to read each product's log.
tags:
  - fundamentals
  - beta
---

# Audit log

Navixy GraphQL API records who created, changed, or deleted a record, and when. The other queries tell you what your data is now. The audit log tells you how it changed, so use it when you need to:

* Find out who made a change, such as who deleted a device, removed a member, or gave an API key its role, and what the values were before.
* Show your users a history of changes to a record or to a whole workspace.
* Match a change with a request in your own logs by the trace ID that they both share.
* Prove to an auditor what changed, when, and who did it. Every event records those three facts, and nobody can edit or delete an event afterwards.

Each product keeps its own audit log and has its own queries to read it. Every log uses the same event format, so the code that reads one log reads the others too.

No query returns the events of several products at once. To show the changes across products, query each product's log and merge the pages by `occurredAt` in your app.

{% hint style="info" %}
Currently, audit is implemented in Business Data Repository, Billing, IAM, and Console. Tracking and IoT Logic don't record audit events.
{% endhint %}

## How the audit log works

A product records one audit event for each change it saves. The event is saved together with the change: when the event can't be recorded, the change isn't saved either. An event never changes after it's recorded, and every product keeps its events indefinitely.

### What one event records

An event is the history log of one change: who made it, which record it changed, what happened to the record, and its values before and after.

The example below is one event from the Console log with every field selected. A user renamed a workspace. The table after the example explains each field.

The record that changed is called an entity in the reference and an aggregate in the field names: `aggregateType` and `aggregateId` describe it, and the record history takes its ID as `entityId`.

```json
{
  "id": "019a6b2f-793e-807b-8001-415544540102",
  "occurredAt": "2026-10-20T09:31:07.218Z",
  "eventCode": "workspace_updated",
  "operation": "UPDATED",
  "product": "console",
  "aggregateType": "workspace",
  "aggregateId": "019a6b2f-793e-807b-8001-574b53500001",
  "workspace": {
    "id": "019a6b2f-793e-807b-8001-574b53500001",
    "title": "Northern depot"
  },
  "actorId": "019a6b2f-793e-807b-8001-555345520001",
  "actor": {
    "__typename": "User",
    "id": "019a6b2f-793e-807b-8001-555345520001",
    "title": "Anna Weber"
  },
  "sourceType": "API",
  "agentName": null,
  "ipAddress": "203.0.113.47",
  "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/142.0",
  "traceId": "4bf92f3577b34da6a3ce929d0e0e4736",
  "eventData": {
    "changedFields": {
      "workspace_name": { "old": "North depot", "new": "Northern depot" }
    },
    "additionalContext": null
  }
}
```

{% hint style="warning" %}
The keys inside `eventData` are the product's internal names, not GraphQL field names, and the API doesn't guarantee them. Use them to read a change, not in automated logic.
{% endhint %}

The following table lists every field of an event and what it contains:

| Field | What it contains |
| --- | --- |
| `actor`, `actorId` | Who made the change. `actor` can be `User`, `ApiKey`, `Staff` for a Navixy employee, or `SystemActor` for a change the platform made on its own. `actor` is `null` when the platform can't match the ID to a user, an API key, or a service, for example, for a user who was deleted. `actorId` still has the ID. |
| `aggregateType`, `aggregateId` | Which record changed: its type as a code, such as `asset` or `workspace`, and its ID. |
| `eventCode` | What happened, as the product names it: `device_created`, `role_assigned`, `invoice_issued`. See [Event codes and operations](README.md#event-codes-and-operations). |
| `operation` | What the change did to the record: `CREATED`, `UPDATED`, `DELETED`, or `RESTORED`. `eventType` returns the same value and is deprecated. |
| `product` | The product that recorded the event: `bdr`, `billing`, `iam`, or `console`. |
| `eventData` | The changed fields with their old and new values, as JSON. Business Data Repository and Billing add the record's stored state before and after the change. |
| `occurredAt` | When the change happened. |
| `sourceType` | Where the change came from: `API` for a request to the API, `AGENT` for a call that an AI client made through the MCP server, and `INTERNAL` for a change the platform made on its own. |
| `agentName` | For a call through the MCP server, the AI client's name. For a request with an API key's token, the `navixy_agent_name` that your app sent when it [exchanged its API key for the token](../authentication/README.md#exchange-the-key-for-an-access-token). Your app chooses this label, so it doesn't prove who acted, and it can contain any text. Show it as plain text: a name such as `<script>` must appear as those characters and never run as code on your page. `null` when the token has no agent name. |
| `traceId` | The trace ID of the request that caused the change, for matching the event with your own logs. |
| `ipAddress`, `userAgent` | Where the request came from. |
| `workspace` | The workspace the change belongs to, when the product records one. See [Workspace and organization events](README.md#workspace-and-organization-events). |

[AuditEvent](../core-api-reference/audit.md#auditevent) in the core API reference describes every field, with the filter and order types that all audit queries share. [What eventData contains](../business-data-repository/guides/investigating-changes-with-audit-logs.md#what-eventdata-contains) explains how to read the old and new values.

### Event codes and operations

An event answers "what happened" twice, at two levels of detail. `operation` is one of four values that every product shares, so a generic list can show "Created" or "Deleted" for any event. `eventCode` is the product's own name for the change, such as `workspace_termination_requested`, and tells apart changes that share an operation.

Each product keeps its own codes and adds a code when it starts recording a new kind of change. A code that was recorded once is never renamed or reused, so show a code you don't know as it is. Two products can use the same code, for example `workspace_created` in Business Data Repository and in Console, so read a code together with `product`. [Audit event codes](event-codes.md) lists the codes of every product with their operations.

## Logs by product

Every product has two audit queries, an event list and a record history:

* The event list, `myAuditEvents`, returns every event that the product recorded in your workspace or organization. Use it to see what changed in a period, or what one person or app changed.
* The record history, `myEntityHistory`, returns the events of one record, by the record's ID. Use it to see how one asset, subscription, or role changed over time. It also works for a deleted record, because its events stay in the log.

Both return events newest first by default and accept the same filter, order, and pagination arguments.

In every product, the queries are `myAuditEvents` and `myEntityHistory`. The `my` prefix says what you can read: every audit query returns only the events of the workspace or organization that your token names.

The following table lists the two queries of each product, the events they return, and who can read them:

<table><thead><tr><th width="170">Product</th><th width="210">Event list and record history</th><th>Events it returns</th><th>Who can read them</th></tr></thead><tbody><tr><td>Business Data Repository</td><td><a href="../business-data-repository/api-reference/audit.md#myauditevents">myAuditEvents</a> and <a href="../business-data-repository/api-reference/audit.md#myentityhistory">myEntityHistory</a> under <code>bdr</code></td><td>The changes to the records of your workspace: assets, devices, geo objects, schedules, and the other audited types</td><td>A role with the <code>read</code> action on <code>bdr.audit</code></td></tr><tr><td>Billing</td><td><a href="../billing/api-reference/audit.md#myauditevents">myAuditEvents</a> and <a href="../billing/api-reference/audit.md#myentityhistory">myEntityHistory</a> under <code>billing</code></td><td>The changes to the subscriptions, invoices, balance, and payments of your workspace or of your whole organization with <code>scope: ORGANIZATION</code></td><td>A role with access to Billing. The organization scope needs a role that covers every workspace of the organization.</td></tr><tr><td>IAM</td><td><a href="../iam/api-reference/audit.md#myauditevents">myAuditEvents</a> and <a href="../iam/api-reference/audit.md#myentityhistory">myEntityHistory</a> under <code>iam</code></td><td>The changes to the roles of your organization and their holders</td><td>A role whose product scope includes IAM, the same rule as for the <a href="../iam/api-reference/roles.md">IAM role queries</a></td></tr><tr><td>Console</td><td><a href="../console/api-reference/audit.md#myauditevents">myAuditEvents</a> and <a href="../console/api-reference/audit.md#myentityhistory">myEntityHistory</a> under <code>console</code></td><td>The changes to your organization, its workspaces, members, invitations, and API keys</td><td>An active member of the organization with a role that has the Console administration feature. See <a href="../console/README.md#who-does-what">Who does what</a></td></tr></tbody></table>

[Investigating changes with audit logs](../business-data-repository/guides/investigating-changes-with-audit-logs.md) walks through an investigation in Business Data Repository, and the same steps work in every product.

You don't need to add your workspace and organization as arguments. The API reads them from your access token: the workspace from its `workspace_id` claim and the organization from its `organization_id` claim, as described in [Access token claims](../authentication/README.md#access-token-claims). The IAM and Console queries need the `organization_id` claim; without it they return `PERMISSION_DENIED`. The queries never return the events of another organization.

Each product's queries return that product's events only. The filter has a `products` field because all audit queries share one filter type. Leave it empty, or name only the product whose queries you call. Any other product is refused, as described in [Audit log errors](README.md#audit-log-errors).

## Example: reading Console log

The query below calls the Console event list, [myAuditEvents](../console/api-reference/audit.md#myauditevents), for the latest changes that Console recorded for your organization. The same selection works in every product's event list.

```graphql
query ConsoleChanges {
  console {
    myAuditEvents(first: 20) {
      nodes {
        id
        occurredAt
        eventCode
        operation
        aggregateType
        aggregateId
        actor {
          __typename
          title
        }
        traceId
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

The response is a page of events in the shape shown in [What one event records](README.md#what-one-event-records), limited to the selected fields, newest first, with `pageInfo` for the next page. See [Pagination](../pagination.md) for how to read `pageInfo`.

Select `__typename` on `actor` to tell a `User` from an `ApiKey`, `Staff`, or a `SystemActor`: the actor types have no fields beyond `id` and `title`.

## Workspace and organization events

A record of Business Data Repository belongs to a workspace, while a role or an organization belongs to no workspace. So each product records its events for a workspace, for an organization, or for both. That decides what its queries return and what `workspace` contains:

* Business Data Repository records every event in a workspace. Its queries return the events of the workspace your token names, and `workspace` is always that workspace.
* IAM records its events for an organization and no workspace. Its queries return the events of the organization your token names, and `workspace` is `null`.
* Console records its events for an organization. Its queries return the events of the organization your token names. `workspace` is filled in only for an event belonging to the workspace that your token names. For an event belonging to another workspace of the organization, or no workspace, it's `null`. The filter has no workspace field, so you can't limit the Console log to one workspace. For the events about the workspace itself, filter by `aggregateIds` with the workspace ID.
* Billing records each event in a workspace. Once Console has linked that workspace to your organization in Billing, the event also records the organization. Use the `scope` argument to choose which events to read:
  * `WORKSPACE`, the default, returns the events in your workspace.
  * `ORGANIZATION` returns the events in all your organization's workspaces.

  An event recorded before Console linked its workspace has no organization, so only `WORKSPACE` returns it.

For example, the query below calls the Billing event list, [myAuditEvents](../billing/api-reference/audit.md#myauditevents), for every workspace of your organization:

```graphql
query OrganizationBillingChanges {
  billing {
    myAuditEvents(scope: ORGANIZATION, first: 20) {
      nodes {
        occurredAt
        eventCode
        workspace {
          title
        }
        actor {
          __typename
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
    "billing": {
      "myAuditEvents": {
        "nodes": [
          {
            "occurredAt": "2026-10-21T10:02:44.906Z",
            "eventCode": "subscription_created",
            "workspace": { "title": "Southern depot" },
            "actor": { "__typename": "User", "title": "Anna Weber" }
          },
          {
            "occurredAt": "2026-10-20T15:40:12.377Z",
            "eventCode": "manual_payment_created",
            "workspace": { "title": "Northern depot" },
            "actor": { "__typename": "Staff", "title": "Staff" }
          }
        ]
      }
    }
  }
}
```

The two events belong to different workspaces of the organization. With the default `WORKSPACE` scope, the same query returns only the events of the workspace your token names.

## Fetching one event by ID

[AuditEvent](../core-api-reference/audit.md#auditevent) implements `Node`, but [node](../core-api-reference/common.md#node-query) and [nodes](../core-api-reference/common.md#nodes) return the events of Business Data Repository and Billing only, a Billing event when your role includes Billing. An event ID doesn't say which product recorded the event, so the API looks in one place. To get an IAM or Console event again, call that product's record history, [IAM](../iam/api-reference/audit.md#myentityhistory) or [Console](../console/api-reference/audit.md#myentityhistory), with the record's ID. Limit the period with `from` and `to` and pick the event by its `id` in your code.

For example, you stored the ID of a role-related IAM event, with the role ID and the event time. The query below calls the IAM record history, [myEntityHistory](../iam/api-reference/audit.md#myentityhistory), for the events of that role in the minute around that time:

```graphql
query RoleEventsAroundOneTime {
  iam {
    myEntityHistory(
      entityId: "019a6b2f-793e-807b-8001-49524f4c0004"
      filter: {
        from: "2026-10-22T13:05:00.000Z"
        to: "2026-10-22T13:06:00.000Z"
      }
      first: 20
    ) {
      nodes {
        id
        occurredAt
        eventCode
        actor {
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
    "iam": {
      "myEntityHistory": {
        "nodes": [
          {
            "id": "019a6b2f-793e-807b-8001-415544540210",
            "occurredAt": "2026-10-22T13:05:31.642Z",
            "eventCode": "role_assigned",
            "actor": { "title": "Anna Weber" }
          },
          {
            "id": "019a6b2f-793e-807b-8001-415544540209",
            "occurredAt": "2026-10-22T13:05:29.118Z",
            "eventCode": "role_updated",
            "actor": { "title": "Anna Weber" }
          }
        ]
      }
    }
  }
}
```

The response lists every event of the role in that minute, latest first. Find the one whose `id` matches the ID you stored.

## Filtering, ordering, and pagination

The filter narrows the events by actor, record type, record ID, event code, operation, source type, product, trace ID, and period. Values within one field are combined with OR, and separate fields are combined with AND. An empty list is ignored. The fields are listed in [AuditEventFilter](../core-api-reference/audit.md#auditeventfilter).

Four fields search by text instead of an exact value: `actorIdContains`, `aggregateTypeContains`, `aggregateIdContains`, and `traceIdContains`. Each field returns the events whose value contains the text you send, in any letter case. You can search with only part of an ID: `aggregateIdContains: "574b5350"` returns the events of every record whose ID contains these characters. They don't work with names: an event stores no name, so the name of a person, an API key, or an agent returns no matches. A value is trimmed, a blank value is ignored, and a value over 255 characters is refused. Text searches are slower than the other filters. When few events contain the text, the API reads through many older events to fill a page. A read that takes longer than five seconds is refused, and the error says how to narrow it: a shorter period, an exact value instead of a text search, or one product. A text search also adds 50 to the request's complexity, so send one text search per request. If your app searches as the user types, send the request only after the user stops typing.

`eventCodes` matches each code exactly as the product recorded it, letter case included. `operations` filters by the four operations. The deprecated `eventTypes` does the same, and naming both in one filter is refused.

`from` and `to` are inclusive and compare against `occurredAt`. Send them as ISO 8601 times with a timezone, such as `2026-10-22T13:05:00.000Z`.

`OCCURRED_AT` is the only field you can order by, in either direction. The default is newest first.

Every audit query follows the cursor pagination described in [Pagination](../pagination.md): a page has 20 events by default and 100 at most. This applies to the IAM and Console audit queries too, although the other IAM and Console lists paginate differently.

## New events

No subscription or webhook delivers audit events. To show new events, repeat the event list query with `from` set to the `occurredAt` of the newest event you have, and `orderBy` ascending. Because `from` is inclusive, the first event of the response is the one you already have: skip it by its `id`. Several events can share one `occurredAt`, so compare IDs, not only times.

## Audit log errors

Audit queries are read-only, so they return the standard errors described in [Error handling](../error-handling.md). The following table lists the errors to expect, their causes, and how to resolve them:

| Error | Cause | How to resolve it |
| --- | --- | --- |
| [`VALIDATION_ERROR` (400)](../error-handling.md#validation-error-400) | `products` names a product other than the one the query reads, an ID has the wrong format, or `first` or `last` is out of range | Leave `products` empty, check the ID, and keep `first` and `last` between 1 and 100 |
| [`VALIDATION_ERROR` (400)](../error-handling.md#validation-error-400) | The filter names both `operations` and `eventTypes`, a code in `eventCodes` has a space or another character a code can't have, or a text search is over 255 characters or has a control character. `field` names the input | Send `operations` only, and check the value that `field` names |
| [`VALIDATION_ERROR` (400)](../error-handling.md#validation-error-400) | The read took longer than five seconds. The error has no `field`, and its message says how to narrow the read | Shorten the period, replace a text search with an exact value, or read one product |
| [`QUERY_TOO_COMPLEX` (400)](../limits.md#query-complexity) | The request has several text searches. Each adds 50 to the request's complexity | Send one text search per request, or combine the texts in one filter |
| [`PERMISSION_DENIED` (403)](../error-handling.md#permission-denied-403) | Your role doesn't allow the read operation. An error from IAM or Console lists the product name in `dependency`. For the `ORGANIZATION` scope of Billing, `field` is `scope`: your token names no organization or your role doesn't cover every workspace of the organization | Ask your organization's administrator for a role that allows read operations, as described in [Roles and products](../authentication/roles-and-products.md) |
| `SERVICE_UNAVAILABLE` (503) | IAM or Console didn't answer. `dependency` names the service | Retry after a short delay |

## Best practices

1. Send the `traceparent` header with your requests. Every event that a request causes gets its trace ID, so you can find the request in your own logs. See [Match audit events with your application logs](../business-data-repository/guides/investigating-changes-with-audit-logs.md#match-audit-events-with-your-application-logs).
2. Read `operation` and filter with `operations`. `eventType` and `eventTypes` return the same values, but they are deprecated and will be removed.
3. Show an event code you don't know as it is, together with its `operation`. A product can add codes at any time.
4. To show one list across products, query each product in parallel and merge the pages by `occurredAt` in your app. Show only the events that are newer than the oldest event on every product's current page. When a product's page ends, fetch its next page before you show more. Without this rule, the events at every page boundary appear out of order. For a small organization, read every page of each product first, then sort once.
5. Request `total { count }` only when you show a total. The count needs a second database query, which is slower with a text search. To page, check `pageInfo.hasNextPage` instead.
6. Store the record ID and the time of an event, not only its ID. `node` returns Business Data Repository and Billing events only, so a record ID and a period are what gets you an IAM or Console event back.

## See also

* [Audit event codes](event-codes.md): The codes of every product with their operations
* [Audit](../core-api-reference/audit.md): The event type, its filter, and its order, shared by every product
* [Investigating changes with audit logs](../business-data-repository/guides/investigating-changes-with-audit-logs.md): Find who changed a record, what they changed, and when
* [Getting started](../getting-started.md): The endpoint, the request format, and the first query
* [Authentication](../authentication/README.md): How to get a token, and the claims that name your workspace and organization
* [Roles and products](../authentication/roles-and-products.md): What your role and your workspace's products allow
* [Pagination](../pagination.md): Page through large result sets with cursors
* [Error handling](../error-handling.md): Understand error structure, codes, and common error scenarios
