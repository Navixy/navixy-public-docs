---
title: Application
description: API calls to create and manage the web applications that a TSP places in the menu of its end users.
---

# Application

A TSP application is a web application of the telematics service provider (TSP) that opens from the menu of end users, either inside the platform interface or in a new browser tab. For example, a TSP can give all its customers a support portal or a service booking form. In the Navixy Admin Panel, these applications are on the **Settings → Applications** tab.

A TSP application has the same fields as a [user application](../../../user-api/resources/commons/user/applications/README.md) that an end user creates in their own account, with two differences:

* `authorization.type` can't be `api_key`. An API key belongs to one user account, and a TSP application opens in many accounts. Use `user_session` to pass the session of the user who opens the application.
* An application doesn't appear in any menu by itself. A [menu preset](../user/preset.md) places it there, with a menu tab whose `destination` is `custom_app` and whose `options.app_id` is the application ID.

## How users get an application

1. Create the application with `dealer/application/create`. It starts turned on.
2. Add a tab with `"destination": "custom_app"` and `"options": {"app_id": <id>}` to a menu preset with [user/menu/preset/update](../user/preset.md#post-panel-user-menu-preset-update). The tab can be the only tab of a menu item, or one of several.
3. Assign the preset to users with [user/menu/preset/assign](../user/preset.md#post-panel-user-menu-preset-assign). To reach all users who have no preset of their own, assign it as the default.

End users read the application through the Platform API: [user/application/list](../../../user-api/resources/commons/user/applications/README.md#post-user-application-list) returns it with `"origin": "dealer"`, and the `user_menu` of [user/get_info](../../../user-api/resources/commons/user/README.md#post-user-get_info) contains the `custom_app` tab. A user can't change or delete a TSP application.

To hide an application from all users without removing it from the presets, turn it off with `dealer/application/enabled/set`. Deleting an application removes it from every preset of the TSP account.

## Application object

{% openapi-schemas spec="admin-panel" schemas="DealerApplication" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

## API actions

API base path: `panel/dealer/application`.

The API sets no limit on the number of applications. The Navixy Admin Panel lets a TSP create up to 30.

***

_required permissions_: `service_settings: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/application/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `service_settings: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/application/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 7 - Invalid parameters - when `authorization.type` is `api_key`, or `url_template` doesn't start with `http://` or `https://`.

***

_required permissions_: `service_settings: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/application/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 7 - Invalid parameters - when `authorization.type` is `api_key`, or `url_template` doesn't start with `http://` or `https://`.
* 201 - Not found in database - when `id` names no application of the current TSP account.

***

_required permissions_: `service_settings: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/application/delete" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in database - when `application_id` names no application of the current TSP account.

***

_required permissions_: `service_settings: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/application/enabled/set" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in database - when `application_id` names no application of the current TSP account.
