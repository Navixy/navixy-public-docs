---
title: History
description: The record of what trackers, assets, and users did, and the operations that read and mark it.
---

# History

The event history is the Navixy platform's record of what happened: a rule that fired, a device that went offline, a user who changed something. It is what a notification feed is built from, and what a reader consults after the fact.

History is read through several lists, split by what the events are about: [trackers](history_tracker.md), [assets](asset-events.md), and [the account's own users](history-user.md). The operations here work on individual entries, reading one and marking entries as read. [Unread events](history_unread.md) covers the unread subset, and [event types](history_type.md) lists what an event can be.

## Tracker history entry

{% openapi-schemas spec="navixy-platform" schemas="HistoryEntry" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The event types an entry's `event` field can carry come from [`history/type/list`](history_type.md#post-history-type-list). Date and time values follow the [platform formats](../../../../general/api-conventions.md#data-types).

For a walkthrough, see [how to work with notifications](../../../guides/rules-notifications/work-with-notifications.md).

## API actions

API base path: `/history`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/history/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

{% openapi-operation spec="navixy-platform" path="/history/mark_read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

{% openapi-operation spec="navixy-platform" path="/history/mark_read_all" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

## More in this section

<!-- endpoint-reference:start -->

#### Tracker events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/tracker/list`](history_tracker.md#post-history-tracker-list) | POST | List tracker history |

#### Event type

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/type/list`](history_type.md#post-history-type-list) | POST | List history event types |

#### Unread events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/unread/list`](history_unread.md#post-history-unread-list) | POST | List unread history |
| [`/history/unread/count`](history_unread.md#post-history-unread-count) | POST | Count unread history |

#### User events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/user/list`](history-user.md#post-history-user-list) | POST | List user history |

#### Asset events

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/history/asset/list`](asset-events.md#post-history-asset-list) | POST | List asset history |
| [`/history/asset/count`](asset-events.md#post-history-asset-count) | POST | Count asset history |

<!-- endpoint-reference:end -->
