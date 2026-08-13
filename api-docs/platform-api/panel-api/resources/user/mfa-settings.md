# Multi-factor authentication settings

> **Work in progress!**\
> This API is a work in progress and may change in future releases.\
> In this version, allowing MFA to a user automatically enables it for them.

## API actions

API path: `panel/user/mfa/settings`.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/mfa/settings/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

Both `target` and `settings` vary by their `type` field. See [Update target](mfa-settings.md#update-target) and [MFA settings](mfa-settings.md#mfa-settings) below.

***

_required permissions_: `users: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/user/mfa/settings/default/read" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

`value` is `null` when the dealer has not configured a default.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/mfa/settings/default/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

The default applies to users who have no explicit setting of their own.

## Types

### MFA settings

{% openapi-schemas spec="admin-panel" schemas="MfaSettings" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

`factor_types` is required when `type` is `allowed`, and must not be empty.

### Update target

{% openapi-schemas spec="admin-panel" schemas="MfaTarget" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

When `type` is `selected`, `ids` must contain between 1 and 100 user IDs. Note this is a tighter limit than the 10000 allowed by [menu preset assignment](preset.md#menu-preset-assignment-type).

### Settings action type

* `allowed` - MFA with the specified factor types is allowed for the users.
* `disallowed` - MFA is disallowed for the users.

### Factor type

* `email` - a multi-digit code sent by email.
