---
tags:
  - reference
  - beta
---

# Stored notifications

The stored notifications of a workspace: the query that lists them with an unread count, and the mutation that marks them as read.

## Queries

### events

Lists the notifications of the caller's workspace that the caller may read: the caller is a user,
  and the assets its `bdr.asset` read access admits, evaluated when the list is read. Reaches
  back the deployment's retention from `publishedAt`; newest by `occurredAt` first unless ordered
  otherwise. A notification the stream missed is here.

  Refused with `PRODUCT_DISABLED` when the workspace does not have Notifications enabled in Console, and
  then with `PERMISSION_DENIED` for an API key, a member of staff or the system actor.

```graphql
events(
    filter: NotificationsEventFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: NotificationsEventOrder = { field: OCCURRED_AT, direction: DESC }
  ): NotificationsEventConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `NotificationsEventFilter` | Filtering options for the returned notifications. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | `NotificationsEventOrder` | The ordering options for the returned notifications. |

**Input types:**

<details>

<summary>NotificationsEventFilter</summary>

Filtering options for the list. All criteria are ANDed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` | Only notifications about these assets; one the caller may not read contributes nothing. |
| `severities` | [[NotificationsSeverity](stream.md#notificationsseverity)!] | Only these severities. |
| `occurredFrom` | [DateTime](../../../core-api-reference/common.md#datetime) | Only events that happened at or after this instant (`occurredAt`). |
| `occurredTo` | [DateTime](../../../core-api-reference/common.md#datetime) | Only events that happened at or before this instant (`occurredAt`). |

</details>

<details>

<summary>NotificationsEventOrder</summary>

Ordering options for the list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [NotificationsEventOrderField](#notificationseventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>NotificationsEventConnection</summary>

A paginated list of NotificationsEvent items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[NotificationsEventEdge](#notificationseventedge)!]! | A list of edges. |
| `nodes` | [[NotificationsEvent](stream.md#notificationsevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | How many notifications the filter matches within the caller's window and access, counted up to one thousand: `EXACT` up to that, `APPROXIMATE` with a `count` of one thousand when there are more. |

</details>

---

### readUpTo

The instant up to which the caller has marked the workspace's notifications as read, by `publishedAt`;
  null before the caller's first `eventsMarkRead` in the workspace. A notification is unread when its
  `publishedAt` is later, so unread and read notifications interleave in a list ordered by `occurredAt`,
  and a client tells them apart by `publishedAt > readUpTo`.

```graphql
readUpTo: DateTime
```

**Output types:**

<details>

<summary>DateTime</summary>

An [ISO 8601](https://www.iso.org/standard/8601.html) datetime string with timezone ([RFC 3339](https://www.rfc-editor.org/rfc/rfc3339.html)). Example: `2024-01-15T10:30:00Z`.

</details>

---

### unreadCount

How many notifications `events` would list that were published after `readUpTo`, every one of them
  before the first mark; the same window and access as the list, counted up to one thousand: `EXACT` up
  to that, `APPROXIMATE` with a `count` of one thousand when there are more.

```graphql
unreadCount: CountInfo!
```

**Output types:**

<details>

<summary>CountInfo</summary>

Information about the total count of items in a connection.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `count` | `Int!` | The count of items matching the filter. |
| `precision` | [CountPrecision](../../../core-api-reference/common.md#countprecision)! | The precision level of the count value. |

</details>

---

## Mutations

### eventsMarkRead

Moves the caller's "read up to" mark of the workspace forward to `upTo` and returns the stored mark.
  A mark never moves backwards, so a stale tab cannot undo a newer one; `unreadCount` counts the
  notifications published after it. Refused with `PRODUCT_DISABLED` and `PERMISSION_DENIED` as
  `Query.notifications` is.

```graphql
eventsMarkRead(input: NotificationsEventsMarkReadInput!): NotificationsEventsMarkReadPayload!
    @effect(kinds: [NONE])
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `NotificationsEventsMarkReadInput!` |  |

**Input types:**

<details>

<summary>NotificationsEventsMarkReadInput</summary>

The input of `NotificationsMutation.eventsMarkRead`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `upTo` | [DateTime](../../../core-api-reference/common.md#datetime)! | The largest `publishedAt` the client has shown, not the client's clock: a mark taken from the clock would count as read what was published between the client's read of the list and the click. An instant past the server's clock is cut to the clock. |

</details>

**Output types:**

<details>

<summary>NotificationsEventsMarkReadPayload</summary>

What `eventsMarkRead` answers.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `readUpTo` | [DateTime](../../../core-api-reference/common.md#datetime)! | The caller's mark in the workspace as stored: the later of the one stored before and the one given, the given one cut to the server's clock. |

</details>

---

## Objects

<a id="notificationseventsmarkreadpayload"></a>

### NotificationsEventsMarkReadPayload

What `eventsMarkRead` answers.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `readUpTo` | [DateTime](../../../core-api-reference/common.md#datetime)! | The caller's mark in the workspace as stored: the later of the one stored before and the one given, the given one cut to the server's clock. |

---

## Inputs

<a id="notificationseventfilter"></a>

### NotificationsEventFilter

Filtering options for the list. All criteria are ANDed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `assetIds` | `[ID!]` | Only notifications about these assets; one the caller may not read contributes nothing. |
| `severities` | [[NotificationsSeverity](stream.md#notificationsseverity)!] | Only these severities. |
| `occurredFrom` | [DateTime](../../../core-api-reference/common.md#datetime) | Only events that happened at or after this instant (`occurredAt`). |
| `occurredTo` | [DateTime](../../../core-api-reference/common.md#datetime) | Only events that happened at or before this instant (`occurredAt`). |

---

<a id="notificationseventorder"></a>

### NotificationsEventOrder

Ordering options for the list.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [NotificationsEventOrderField](#notificationseventorderfield)! | The field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="notificationseventsmarkreadinput"></a>

### NotificationsEventsMarkReadInput

The input of `NotificationsMutation.eventsMarkRead`.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `upTo` | [DateTime](../../../core-api-reference/common.md#datetime)! | The largest `publishedAt` the client has shown, not the client's clock: a mark taken from the clock would count as read what was published between the client's read of the list and the click. An instant past the server's clock is cut to the clock. |

---

## Enums

<a id="notificationseventorderfield"></a>

### NotificationsEventOrderField

Fields available for ordering the list; the id breaks ties, so a page is stable.

| Value | Description |
| ----- | ----------- |
| `OCCURRED_AT` | By when the event happened. |

---

## Pagination types

<a id="notificationseventconnection"></a>

### NotificationsEventConnection

A paginated list of NotificationsEvent items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[NotificationsEventEdge](#notificationseventedge)!]! | A list of edges. |
| `nodes` | [[NotificationsEvent](stream.md#notificationsevent)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | How many notifications the filter matches within the caller's window and access, counted up to one thousand: `EXACT` up to that, `APPROXIMATE` with a `count` of one thousand when there are more. |

---

<a id="notificationseventedge"></a>

### NotificationsEventEdge

An edge in the NotificationsEvent connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [NotificationsEvent](stream.md#notificationsevent)! | The notification at the end of the edge. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
