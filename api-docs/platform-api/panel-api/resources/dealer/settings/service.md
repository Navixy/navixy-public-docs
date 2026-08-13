---
title: Service
description: API calls to read and update panel's service settings.
---

# Service Settings

Service settings in Navixy allow administrators to configure and customize various aspects of the monitoring service. These settings control the appearance and functionality of the platform, such as default maps, user interfaces, branding elements, and user registration options. By adjusting these parameters, administrators can tailor the platform to better meet the needs of their users and reflect their company's branding and operational requirements.

## Service settings object

{% openapi-schemas spec="admin-panel" schemas="ServiceSettings" grouped="true" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

Notes on individual fields:

* `locale`, `currency`, `maps`, `allowed_maps`, and `default_map.type` use platform-wide enums described in the [data types](../../../../user-api/backend-api/#data-types) section.
* Branding image paths, that is `favicon`, `logo`, `app_logo`, `document_logo`, `monitoring_logo`, `login_wallpaper`, and `desktop_wallpaper`, are read-only here. Change them through the [branding image](image.md) operations.
* `default_user_time_zone` is a [time zone ID](../../timezone.md) applied to users created via [user/upload](../../user/README.md#post-panel-user-upload).
* `email_special` is the same underlying value as `email_special` in the [notification settings](notification.md#notification-settings-object).

## API actions

API path: `panel/dealer/settings/service`.

***

_required permissions_: `service_settings: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/settings/service/read" method="post" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

[General](../../../../user-api/backend-api/errors.md#error-codes) types only.

***

_required permissions_: `service_settings: "update"`.

{% hint style="danger" %}
This is a full replacement, and it covers more fields than the request schema lists. Any writable setting you omit is reset to its default, including settings the schema does not enumerate.

Sending only the fields documented as parameters was observed to silently clear `email_special` and flip `no_auto_create_rules` from `true` to `false`.

Only `allow_registration`, `show_mobile_apps`, and `default_user_time_zone` are preserved when omitted. Every other writable setting is reset.

Always call [Read service settings](service.md#post-panel-dealer-settings-service-read) first, change the fields you intend to change, and send everything back. Do not build a request from the schema alone.
{% endhint %}

Wallpapers, logos, and favicons cannot be edited here. Use the [branding image](image.md) operations instead.

{% openapi-operation spec="admin-panel" path="/panel/dealer/settings/service/update" method="post" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 247 - Entity already exists(409) - when `domain` already used by other dealer.
