---
title: Settings
description: An account's own preferences for timezone, formats, balance alerts, and file storage.
---

# Settings

User settings are the account's own preferences: the timezone and formats everything else is rendered in, when to warn about a low balance, and how uploaded files are handled.

Two of these are stored separately from the rest and are visible only to a master user, which is worth knowing before reading the response. `balance_alert_settings` and `file_storage_settings`, along with `first_user_balance_warning_period` and `second_user_balance_warning_period`, are **absent entirely** unless the caller holds the `admin` right, rather than merely being read-only.

The rest of the `settings` object is filtered by the dealer's GIS package before it is returned, so a field can be absent or null on a platform with a restricted package.

## Settings object

{% openapi-schemas spec="navixy-platform" schemas="UserSettings,BalanceAlertSettings,FileStorageSettings" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

`balance_alert_settings.emails` lists the addresses that receive balance alerts, and an empty array turns those alerts off. `file_storage_settings.auto_overwrite` defaults to `false`.

## API actions

API base path: `/user/settings`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `admin` for the `balance_alert_settings` and `file_storage_settings` fields, available only to master users.

{% openapi-operation spec="navixy-platform" path="/user/settings/read" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% hint style="warning" %}
This operation **replaces** the stored settings rather than merging into them. Only `time_zone` is required, and every writable field left out of the request is cleared, including `date_format` and `hour_mode`. Send every field you intend to keep.

`balance_alert_settings` and `file_storage_settings` are stored separately and do survive omission.
{% endhint %}

_Required sub-user rights:_ `admin` for the `balance_alert_settings` and `file_storage_settings` fields, available only to master users.

{% openapi-operation spec="navixy-platform" path="/user/settings/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% hint style="warning" %}
Send the settings fields at the **top level** of the request body, not wrapped in a `file_storage_settings` object. A wrapped body is accepted and answers `success: true`, but writes defaults instead of the values supplied.
{% endhint %}

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/user/settings/file_storage/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 13 - Operation not permitted, when the user has insufficient rights.

## More in this section

<!-- endpoint-reference:start -->

#### User UI settings

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/settings/ui/read`](ui.md#post-user-settings-ui-read) | POST | Read UI setting |
| [`/user/settings/ui/update`](ui.md#post-user-settings-ui-update) | POST | Update UI setting |

<!-- endpoint-reference:end -->
