---
title: User audit log
description: What the account's users did, so an owner can review activity across sub-users.
---

# User audit log

The audit log records what the account's users did, which is how an account owner reviews the activity of everyone added through the Access rights section.

It covers the current user and their [sub-users](../../subuser/README.md), so a sub-user calling it sees only their own records.

## Audit object

{% openapi-schemas spec="navixy-platform" schemas="AuditLogRecord" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/user/audit/log`.

Results can be sorted by:

* `action`
* `action_date`, by date only, ignoring the time
* `action_datetime`, by date including the time
* `user`, by the sub-user's last, first, and middle name rather than by ID
* `host`

Without a sort parameter, the result is sorted as though `["action_date=asc"]` had been given.

{% hint style="info" %}
A request with an empty body reports `interval` as the missing required parameter. There is no such parameter. It is the internal name of the field holding both dates, and the parameters to send are `from` and `to`.
{% endhint %}

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/user/audit/log/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
