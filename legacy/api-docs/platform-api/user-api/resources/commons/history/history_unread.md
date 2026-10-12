---
title: Unread events
description: The history entries a user has not read yet, listed or counted.
---

# Unread events

These two operations cover the unread subset of the [event history](README.md): one lists the entries the user has not read, the other counts them, which is what an unread badge needs.

Entries are marked read with [`history/mark_read`](README.md#post-history-mark_read) and [`history/mark_read_all`](README.md#post-history-mark_read_all). The default and maximum `limit` is the `history.max_limit` value that [`dealer/get_ui_config`](../dealer.md#post-dealer-get_ui_config) reports.

For a walkthrough, see [all unread events of a user](../../../guides/rules-notifications/work-with-notifications.md#all-unread-events-of-a-user).

## API actions

API base path: `/history/unread`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/history/unread/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 212 - Requested period is not available on the current plan.

***

{% openapi-operation spec="navixy-platform" path="/history/unread/count" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
