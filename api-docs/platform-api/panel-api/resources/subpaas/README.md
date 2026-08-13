---
description: API calls to interact with Subpaases.
---

# Subpaas

## Subdealer (Sub PaaS) actions

In Navixy, a Subdealer (Sub PaaS) account is designed for dealers who need to manage multiple accounts that provide services independently. This feature is ideal for dealers who act as larger distributors or service providers. With Sub PaaS accounts, dealers can create and manage additional sub-accounts, each with its own users, devices, and settings.

These sub-accounts function similarly to the main dealer account but allow for more granular control and management. This setup is perfect for servicing other dealers (subdealers) or larger enterprises that require separate PaaS accounts, such as a service run on their own domain. It simplifies operations of multiple independent organizations, while maintaining oversight from the main dealer account.

## Subdealer (Sub PaaS) object

The read operation returns the full object. The list operation returns a smaller field set plus per-sub-dealer user and device counts.

{% openapi-schemas spec="admin-panel" schemas="Subpaas,SubpaasListEntry" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

## API actions

API base path: `panel/subpaas`.

Every operation here requires the `subpaas` permission, and is available only to dealers whose contract type and plan permit sub-dealer accounts.

***

_required permissions_: `subpaas: "create"`.

A newly created sub-dealer starts in `INITIAL_BLOCK` status.

{% hint style="warning" %}
Creating a sub-dealer sends email. The platform notifies both the parent dealer and the platform managers.
{% endhint %}

{% include "../../../../.gitbook/includes/password-requirements.md" %}

{% openapi-operation spec="admin-panel" path="/panel/subpaas/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 13 – If the dealer:
  * is not of the PaaS type.
  * has a status other than `NOT_BLOCKED`.
  * has a plan that does not allow SubPaaS accounts.

***

_required permissions_: `subpaas: "read"`.

The parent dealer is taken from the session, so only your own sub-dealers are returned.

{% openapi-operation spec="admin-panel" path="/panel/subpaas/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `subpaas: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/subpaas/read" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `subpaas: "update"`.

Unlike create, this operation requires `subpaas_id` and `block_type`. It does **not** accept a password: use [change password](password.md) instead.

Both `link_monitoring` and `email` must be unique across dealers. A clash returns error code 7 naming the offending parameter, rather than a 409.

{% openapi-operation spec="admin-panel" path="/panel/subpaas/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 13 –
  * The dealer is not PaaS.
  * The dealer has a status other than `NOT_BLOCKED`.
  * SubPaaS accounts are not permitted for the dealer.
  * `block_type` is `DELETED`.
  * The found SubPaaS is in `DELETED` status.
  * The found SubPaaS is not in `INITIAL_BLOCK` status and `block_type` is `INITIAL_BLOCK`.
  * The found SubPaaS is in `INITIAL_BLOCK` status and `block_type` is not `INITIAL_BLOCK`.
