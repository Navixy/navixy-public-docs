---
title: Account
description: API calls on getting the panel's hash, getting permissions and logout.
---

# Account

## API actions

API path: `panel/account`.

***

{% openapi-operation spec="admin-panel" path="/panel/account/auth" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

The `permissions` object in the response is described under panel account [permissions](../getting-started.md#admin-panel-api-permissions).

#### Errors

* 11 - Access denied - if dealer blocked.
* 12 - Dealer not found.

***

_required permissions_: none beyond a valid session.

{% openapi-operation spec="admin-panel" path="/panel/account/get_permissions" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

[General](../../user-api/backend-api/errors.md#error-codes) types only.

***

_required permissions_: none beyond a valid session.

{% openapi-operation spec="admin-panel" path="/panel/account/logout" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

[General](../../user-api/backend-api/errors.md#error-codes) types only.
