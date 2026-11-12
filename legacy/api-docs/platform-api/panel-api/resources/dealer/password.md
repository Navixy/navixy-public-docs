---
title: Update password
description: API call to update the password of the telematics service provider (TSP) account.
---

# Password

## API actions

API base path: `panel/dealer/password`.

***

_required permissions_: `password: "update"`.

{% include "../../../../.gitbook/includes/password-requirements.md" %}

The complexity rules actually enforced for a given TSP account are returned as `password_policy` by [dealer/get\_info](README.md#post-panel-dealer-get_info).

{% openapi-operation spec="admin-panel" path="/panel/dealer/password/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

{% hint style="warning" %}
On success, every panel session for this TSP account is deleted, including the one that made the request. The session hash stops working immediately and you must authenticate again before making further calls.
{% endhint %}

#### Errors

* 12 - Dealer not found - if the TSP account record is missing.
* 245 - New password must be different - if `old_password` = `new_password`.
* 248 - Wrong password - if `old_password` is wrong.
* 294 - Password was recently used - if `new_password` matches one of the recently used passwords.
