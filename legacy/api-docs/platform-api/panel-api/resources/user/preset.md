---
title: Menu preset
description: API calls to work with user menu presets.
tags:
  - legacy
---

# Menu preset

API calls to work with the customizable sidebar structure for end users.\
Customizable sidebar allows you to create flexible user menus by adding, rearranging, and renaming menu items and tabs.\
Menu presets act as templates that can be assigned to users.

## Entity description

### Menu preset

{% openapi-schemas spec="admin-panel" schemas="MenuPreset" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

Note the asymmetry between the two layout areas: `main` is an **array** of groups, while `footer` is a **single** group.

### Menu group

{% openapi-schemas spec="admin-panel" schemas="MenuGroup" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

### Menu item

{% openapi-schemas spec="admin-panel" schemas="MenuItem" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

### Menu tab

{% openapi-schemas spec="admin-panel" schemas="MenuTab" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

All `title` values on presets, groups, items, and tabs are **localization keys** such as `common.menu.dashboard`, not display strings. The interface resolves them against the user's locale. A title that is not a localization key is shown as it is, as with the tabs of TSP applications below.

#### Tabs of TSP applications

A tab whose `destination` is `custom_app` opens a [TSP application](../dealer/application.md) of the current TSP account. Its `options.app_id` names the application:

```json
{
  "title": "Service booking",
  "destination": "custom_app",
  "options": {
    "app_id": 12
  }
}
```

The tab can be the only tab of a menu item or one of several. Users see the tab only while the application is turned on. A turned-off application stays in the preset and appears again when it is turned on. Deleting the application removes its tabs from every preset of the TSP account. A menu item left without tabs is removed, and so is a menu group left without menu items.

### Menu preset assignment

{% openapi-schemas spec="admin-panel" schemas="MenuPresetListEntry" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

#### Preset owner

`owner` reports who defined the preset:

* `dealer` - created by the telematics service provider (TSP), and editable by them.
* `platform` - supplied by Navixy. Visible to the TSP but not editable.

### Menu preset assignment type

{% openapi-schemas spec="admin-panel" schemas="MenuPresetAssignmentTarget" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

## API actions

API path: `panel/user/menu/preset`.

***

_required permissions_: `users: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/user/menu/preset/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `users: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/user/menu/preset/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 7 - Invalid parameters - if a `custom_app` tab names no application of the current TSP account. The `errors` array names the tab by its position, such as `preset.main[0].items[2].tabs[0]`, with the error `forbidden`.
* 201 - Not found in the database - if a referenced menu item or destination does not exist.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/menu/preset/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 7 - Invalid parameters - if a `custom_app` tab names no application of the current TSP account. The `errors` array names the tab by its position, such as `preset.main[0].items[2].tabs[0]`, with the error `forbidden`.
* 201 - Not found in the database - if there is no preset with the specified ID.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/menu/preset/delete" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if there is no preset with the specified ID.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/menu/preset/assign" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if there is no preset with the specified ID.

## Available menu items

_required permissions_: `users: "read"`.

{% hint style="info" %}
This operation lives at `panel/user/menu/item/list`, outside the `menu/preset/` path used by the rest of this page.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/user/menu/item/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

Use this to discover the destinations available when composing a preset.
