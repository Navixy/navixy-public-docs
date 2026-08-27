---
title: Audit
description: Record that a user opened or returned to the interface, for the audit log.
---

# Audit

This operation records that a user is active. A client calls it when the user opens the interface, or activates a browser tab that has been idle for more than two hours, and the record then appears in the [audit log](audit_log.md#post-user-audit-log-list).

It works only with a standard user session. Calling it with an API key returns error 4, "User or API key not found or session ended", even though the key itself is valid.

## API actions

API base path: `/user/audit`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/user/audit/checkin" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

## More in this section

<!-- endpoint-reference:start -->

#### User audit log

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/audit/log/list`](audit_log.md#post-user-audit-log-list) | POST | List audit log records |

<!-- endpoint-reference:end -->
