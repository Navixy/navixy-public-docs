---
title: Schedule proposals
description: A preview of the tasks and routes a schedule will create over a date range.
---

# Schedule proposals

A proposal is a preview: it reports the tasks and routes that the [schedules](README.md) would create over a date range, without creating any of them. It answers what next week looks like before next week arrives.

Every filter is optional. Leaving `trackers`, `filter`, `from`, or `to` out, or sending it as null, simply drops that condition rather than matching nothing.

## API actions

API base path: `/task/schedule/proposal`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

If `trackers`, `filter`, `from` or `to` is not passed or _null_ then appropriate condition not used to filter results.

***

{% openapi-operation spec="navixy-platform" path="/task/schedule/proposal/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
