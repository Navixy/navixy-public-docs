---
title: User UI settings
description: A key-value store for client application state, kept per user but not guaranteed.
---

# User UI settings

These settings are a key-value store for client applications that use the API, holding whatever state an application wants to survive between sessions, such as a saved layout or a filter.

Treat it like browser local storage rather than like a database. Storage is long-term but **not guaranteed**: once the quota is exhausted, stored data can be deleted.

Two behaviours are worth knowing before building on it. A key that has never been written returns `value: null` with HTTP 200, which is not an error. And a setting can only be overwritten, never removed: `value` may not be null, and there is no delete operation.

## API actions

API base path: `/user/settings/ui`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/user/settings/ui/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/user/settings/ui/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 268 - Over quota. The storage available to the user for these settings is exhausted, and no new setting can be added until some stored data is removed.
