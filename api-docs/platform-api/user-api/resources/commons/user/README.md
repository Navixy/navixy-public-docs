---
title: User
description: Authenticating, reading who the session belongs to, and ending it.
---

# User

A user is an account on the Navixy platform. The operations here cover its life cycle at the edges an integration touches: turning credentials into a session, reading who that session belongs to and what their plan allows, and ending it.

Everything a user owns has its own page: [settings](settings/README.md), [password](password.md), [sessions](session/README.md), [personal info](personal_info.md), and the [audit log](audit/README.md). [Sub-users](../subuser/README.md) are separate accounts under this one.

## User object

{% openapi-schemas spec="navixy-platform" schemas="UserInfo,TariffRestrictions,PaasSettings" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

Definitions the response refers to but does not repeat:

* `paas_settings` - object. The same as `settings` in the [`dealer/get_ui_config` response](../dealer.md#post-dealer-get_ui_config).
* `user_info.master` - object. Returned only when the current user is a sub-user. Its fields mean the same as in `user_info`, but describe the master account.
* `features` - string array. The [dealer features](../dealer.md#dealer-features) that are enabled.
* `privileges.rights` - string array. The rights granted to a sub-user, described in [security group rights](../subuser/security_group.md#security-group-rights).
* `user_menu` - a [menu preset](../../../../panel-api/resources/user/preset.md#menu-preset). Its `account`, `main`, `applications`, and `footer.items` entries are [menu item](../../../../panel-api/resources/user/preset.md#menu-item) arrays.
* `tariff_restrictions` - the tariff restrictions object, which [`user/get_tariff_restrictions`](README.md#post-user-get_tariff_restrictions) also returns on its own.

## API actions

API base path: `/user`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

Authenticating needs no session of its own, and is available at the `UNAUTHORIZED` access level.

{% hint style="info" %}
Prefer an [API key](../api-keys.md) to a user session hash for anything long-lived. See [Platform authentication](../../../authentication.md) for how to obtain and send one.
{% endhint %}

When multi-factor authentication is enabled, the response carries `type: "second_factor_required"` and a hash usable only for the [authentication code](auth/README.md) operations until a code is verified.

{% openapi-operation spec="navixy-platform" path="/user/auth" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 11 - Access denied, when the dealer is blocked.
* 102 - Wrong login or password.
* 103 - User not activated.
* 104 - Logins limit exceeded. Reuse an existing session instead, see [`user/session/renew`](session/README.md#post-user-session-renew).
* 105 - Login attempts limit exceeded. Try again later.

***

Activation takes the session hash from the activation link emailed to the user, and works only for master users.

{% hint style="warning" %}
This operation accepts **only** the session hash from a registration email. Any other hash returns error 4, "User or API key not found or session ended". The only other thing that hash can do is create, read, and delete API keys.
{% endhint %}

{% openapi-operation spec="navixy-platform" path="/user/activate" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Resending an activation link needs no session of its own, and is available at the `UNAUTHORIZED` access level.

{% openapi-operation spec="navixy-platform" path="/user/resend_activation" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no user has the given login.
* 209 - Failed sending email.
* 264 - Timeout not reached, when the previous activation link was generated less than 5 minutes ago, or within whatever timeout the server is configured with.
* 265 - Already done, when the account is already activated.

***

{% openapi-operation spec="navixy-platform" path="/user/get_info" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/user/get_tariff_restrictions" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

Logging out works only with a standard user session. Calling it with an API key returns error 4, "User or API key not found or session ended", even though the key itself is valid.

{% openapi-operation spec="navixy-platform" path="/user/logout" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

## More in this section

<!-- endpoint-reference:start -->

#### User password

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/password/change`](password.md#post-user-password-change) | POST | Change password |
| [`/user/password/set`](password.md#post-user-password-set) | POST | Set password |

#### User personal info

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/personal_info/update`](personal_info.md#post-user-personal_info-update) | POST | Update personal info |

#### Audit

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/audit/checkin`](audit/README.md#post-user-audit-checkin) | POST | Record user check-in |

#### User audit log

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/audit/log/list`](audit/audit_log.md#post-user-audit-log-list) | POST | List audit log records |

#### User authentication code

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/auth/code/verify`](auth/README.md#post-user-auth-code-verify) | POST | Verify auth code |
| [`/user/auth/code/resend`](auth/README.md#post-user-auth-code-resend) | POST | Resend auth code |

#### Session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/renew`](session/README.md#post-user-session-renew) | POST | Renew session |

#### Delivery session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/delivery/create`](session/delivery.md#post-user-session-delivery-create) | POST | Create delivery session |
| [`/user/session/delivery/read`](session/delivery.md#post-user-session-delivery-read) | POST | Read delivery session |

#### Push token

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/push_token/bind`](session/push_token.md#post-user-session-push_token-bind) | POST | Bind push token |
| [`/user/session/push_token/delete`](session/push_token.md#post-user-session-push_token-delete) | POST | Delete push token |

#### Settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/settings/read`](settings/README.md#post-user-settings-read) | POST | Read user settings |
| [`/user/settings/update`](settings/README.md#post-user-settings-update) | POST | Update user settings |
| [`/user/settings/file_storage/update`](settings/README.md#post-user-settings-file_storage-update) | POST | Update file storage settings |

#### User UI settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/settings/ui/read`](settings/ui.md#post-user-settings-ui-read) | POST | Read UI setting |
| [`/user/settings/ui/update`](settings/ui.md#post-user-settings-ui-update) | POST | Update UI setting |

#### User applications

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/application/list`](applications/README.md#post-user-application-list) | POST | List user applications |
| [`/user/application/create`](applications/README.md#post-user-application-create) | POST | Create user application |
| [`/user/application/update`](applications/README.md#post-user-application-update) | POST | Update user application |
| [`/user/application/delete`](applications/README.md#post-user-application-delete) | POST | Delete user application |
| [`/user/application/enabled/set`](applications/README.md#post-user-application-enabled-set) | POST | Show or hide a user application |

<!-- endpoint-reference:end -->
