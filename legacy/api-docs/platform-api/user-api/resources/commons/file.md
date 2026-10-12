---
title: File
description: How much of the account's file storage quota is in use.
---

# File

This operation reports how much of the account's file storage quota is used and how much remains.

It is worth calling before reserving a file with any of the `file/create` operations elsewhere in the API, because those fail with error 268 once the quota is exhausted.

## API actions

API base path: `/file`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/file/stats/read" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
