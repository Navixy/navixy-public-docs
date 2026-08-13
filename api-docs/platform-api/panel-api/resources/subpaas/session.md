---
title: Session
description: API call to create a subpaas session.
---

# Subpaas session key

## API actions

API base path: `panel/subpaas/session`.

***

_required permissions_: `subpaas: "create"`.

{% hint style="warning" %}
The returned `hash` grants access to the sub-dealer's panel. Treat it as a credential and handle it exactly as you would a hash from [authentication](../../authentication.md).
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/subpaas/session/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 13 –
  * The dealer is not PaaS type.
  * The dealer has a status other than `NOT_BLOCKED`.
  * The dealer's tariff does not allow SubPaaS accounts.
  * The found SubPaaS is not in `NOT_BLOCKED` status.
