---
title: Task history
description: The record of how a task's fields and state changed over its life.
---

# Task history

The Navixy platform records every change to a [task](README.md)'s fields and its state, and this operation reads that record. It answers who changed what and when, which matters when a task's outcome is disputed after the fact.

## History entry

{% openapi-schemas spec="navixy-platform" schemas="TaskHistoryEntry" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/task/history`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/task/history/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
