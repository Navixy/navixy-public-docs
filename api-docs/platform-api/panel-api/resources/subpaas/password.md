---
title: Password
description: API call to change subpaas password.
---

# Change password

API call to change the password of a Subdealer (SubPaaS) account.

## API actions

API base path: `panel/subpaas/password`.

***

_required permissions_: `subpaas: "update"`.

The current password is not required: a parent dealer can reset a sub-dealer's password directly.

{% include "../../../../.gitbook/includes/password-requirements.md" %}

{% openapi-operation spec="admin-panel" path="/panel/subpaas/password/change" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 13 –
  * The dealer is not paas.
  * The dealer has different status than `NOT_BLOCKED`.
  * The dealer's tariff does not allow subpaases.
  * Found subpaas is in `DELETED` status.
* 294 - Password was recently used - if `new_password` matches one of the recently used passwords.
