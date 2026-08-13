---
title: Activation code
description: API calls for interacting with activation codes used for device registration.
---

# Activation code

Activation codes in Navixy simplify the device activation process by allowing users to set up devices themselves, ensuring they are configured with the appropriate plan and benefits from the start. These codes can include bonuses, free days, and monetary credits to a user's balance upon activation, and are tied to specific plans.

Depending on the dealer's preference, activation codes can be optional or required. Dealers can choose to restrict device activation to only those they supply, preventing users from adding devices purchased elsewhere without an activation code received from the dealer.

## Activation Code Object

{% openapi-schemas spec="admin-panel" schemas="ActivationCode" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

## API actions

API base path: `panel/dealer/activation_code`.

***

_required permissions_: `activation_code: ["read", "create"]`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/activation_code/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - when tariff with `tariff_id` not found for a current dealer.

***

_required permissions_: `activation_code: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/activation_code/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

Entities are returned only if the `filter` string matches one of `code`, `tariff_id`, `device_id`, or `device_type`.

***

Only codes that meet all of the following are changed:

* the `code` is listed in the `codes` parameter,
* it belongs to the current dealer,
* it has not been activated yet,
* it belongs to the same `device_type` as the new plan.

_required permissions_: `activation_code: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/activation_code/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - when a plan with `tariff_id` is not found for the current dealer.
