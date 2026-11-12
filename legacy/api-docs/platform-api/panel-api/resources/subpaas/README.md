---
description: API calls to interact with Subpaases.
---

# Subpaas

## Reseller (Sub PaaS) actions

In Navixy, a reseller (Sub PaaS) account is designed for telematics service providers (TSPs) who need to manage multiple accounts that provide services independently. The Admin Panel calls this feature Resellers, and the API calls it `subpaas`. This feature is ideal for TSPs who act as larger distributors or service providers. With Sub PaaS accounts, TSPs can create and manage additional sub-accounts, each with its own users, devices, and settings.

These sub-accounts function similarly to the main TSP account but allow for more granular control and management. This setup is perfect for servicing resellers or larger enterprises that require separate PaaS accounts, such as a service run on their own domain. It simplifies operations of multiple independent organizations, while maintaining oversight from the main TSP account.

## Reseller (Sub PaaS) object

The read operation returns the full object. The list operation returns a smaller field set plus per-reseller user and device counts.

{% openapi-schemas spec="admin-panel" schemas="Subpaas,SubpaasListEntry" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

## API actions

API base path: `panel/subpaas`.

Every operation here requires the `subpaas` permission, and is available only to TSPs whose contract type and plan permit reseller accounts.

***

_required permissions_: `subpaas: "create"`.

A newly created reseller starts in `INITIAL_BLOCK` status.

{% hint style="warning" %}
Creating a reseller sends email. The platform notifies both the parent TSP and the platform managers.
{% endhint %}

{% include "../../../../.gitbook/includes/password-requirements.md" %}

{% openapi-operation spec="admin-panel" path="/panel/subpaas/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 13 – If the TSP account:
  * is not of the PaaS type.
  * has a status other than `NOT_BLOCKED`.
  * has a plan that does not allow SubPaaS accounts.

***

_required permissions_: `subpaas: "read"`.

The parent TSP is taken from the session, so only your own resellers are returned.

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

Both `link_monitoring` and `email` must be unique across TSP accounts. A clash returns error code 7 naming the offending parameter, rather than a 409.

{% openapi-operation spec="admin-panel" path="/panel/subpaas/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 13 –
  * The TSP account is not PaaS.
  * The TSP account has a status other than `NOT_BLOCKED`.
  * SubPaaS accounts are not permitted for the TSP account.
  * `block_type` is `DELETED`.
  * The found SubPaaS is in `DELETED` status.
  * The found SubPaaS is not in `INITIAL_BLOCK` status and `block_type` is `INITIAL_BLOCK`.
  * The found SubPaaS is in `INITIAL_BLOCK` status and `block_type` is not `INITIAL_BLOCK`.
