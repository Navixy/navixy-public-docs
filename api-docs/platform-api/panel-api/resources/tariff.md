---
title: Tariff
description: API calls for the Tariff resource, covering service plans and device registration defaults.
---

# Tariff

This resource is `tariff` in API paths and is called a plan in the Admin Panel interface. It manages the service plans a dealer offers to its users, and returns each one as a `Plan` object.

## Plan object

{% openapi-schemas spec="admin-panel" schemas="Plan" grouped="true" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-schemas %}

Notes on individual fields:

* `type`, `device_type`, and the map codes inside `map_filter` use platform-wide enums described in the [data types](../../user-api/backend-api/#data-types) section.
* `store_period` uses a short duration form here, such as `1y`, which is **not** the ISO 8601 form used by `store_period` on the [dealer object](dealer/README.md).
* `early_change_price` is omitted from the response when it has no value, in which case users cannot change plan frequently.

## API actions

API path: `panel/tariff`.

***

_required permissions_: `"tariffs": "create"`.

Plan creation counts against the dealer's plan quota.

{% openapi-operation spec="admin-panel" path="/panel/tariff/create" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `"tariffs": "read"`.

{% openapi-operation spec="admin-panel" path="/panel/tariff/list" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

`wholesale_service_prices` reports the prices the dealer itself is charged, and is not part of any individual plan.

***

_required permissions_: `"tariffs": "read"`.

{% openapi-operation spec="admin-panel" path="/panel/tariff/read" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if the plan does not exist or belongs to another dealer.

***

_required permissions_: `tariffs: "update"`.

A plan's `device_type` cannot be changed after creation, so the update object omits it.

{% openapi-operation spec="admin-panel" path="/panel/tariff/update" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

## defaults object

Defaults are applied when a device is registered: which plan it lands on, and what bonus and free period it receives. They are keyed by device type.

{% openapi-schemas spec="admin-panel" schemas="TariffDefaults" grouped="true" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-schemas %}

***

_required permissions_: `tariffs: "read"`.

The defaults sit at the top level of the response under their device-type key, not nested under `value`.

{% openapi-operation spec="admin-panel" path="/panel/tariff/defaults/read" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `tariffs: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/tariff/defaults/update" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}
