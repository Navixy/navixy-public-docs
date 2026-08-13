---
description: Base URLs, authentication, and the resource family index for the Admin Panel API, with a quick endpoint reference for every operation.
---

# Resources

This page indexes the Admin Panel API's operations by resource family. Each family has its own page with the full parameter and response schema for every operation, plus a panel for sending a test request directly from the documentation.

Use this page to find the family you need and jump straight to its operations.

## Authentication

Most operations require a session hash in the `Authorization` header. See [Admin Panel authentication](../authentication.md) for how to obtain one and use it in requests.

## Base URL

The Admin Panel API is accessible via the `panel/` subsection of the API URL. The URLs for different Navixy platforms are as follows:

* European Navixy ServerMate platform: `https://api.eu.navixy.com/v2/panel/`
* American Navixy ServerMate platform: `https://api.us.navixy.com/v2/panel/`
* Middle East Navixy ServerMate platform: `https://api.me.navixy.com/v2/panel/`
* Self-hosted (On-Premise) installations: `https://api.your_domain/panel/`

For example, an `account/auth` call on the European platform uses `https://api.eu.navixy.com/v2/panel/account/auth`.

The query-string and form-encoded body transports both percent-encode their values. Any character in those values outside ASCII codes 32 to 127 must be [URL encoded](https://en.wikipedia.org/wiki/Percent-encoding).

## Resource families

Every operation belongs to one of the 10 resource families below. For how dealers, sub-dealers, users, trackers, and plans relate to each other, see [Key concepts](../getting-started.md#key-concepts).

| Family | Operations for |
| --- | --- |
| [Account](account.md) | Session lifecycle and permission checks |
| [Dealer](dealer/README.md) | Dealer profile, activation codes, and panel settings |
| [Subpaas](subpaas/README.md) | Sub-dealer accounts and their credentials |
| [User](user/README.md) | User accounts, balances, menu presets, and multi-factor authentication |
| [Tracker](tracker.md) | Devices, clones, settings, and plans |
| [Tariff](tariff.md) | Service plans and device registration defaults |
| [Order](order.md) | Equipment orders |
| [Gateways](gateways.md) | Email gateways |
| [Timezone](timezone.md) | Supported time zones |

## Quick endpoint reference

The tables below list every operation grouped by family. Each endpoint links to the page where its full schema and test panel live.

### Account

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/account/auth`](account.md#post-panel-account-auth) | POST | Authenticate a dealer and open a panel session. |
| [`/panel/account/get_permissions`](account.md#post-panel-account-get_permissions) | POST | Get the permissions granted to the current session. |
| [`/panel/account/logout`](account.md#post-panel-account-logout) | POST | End the current session. |

### Dealer

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/dealer/get_info`](dealer/README.md#post-panel-dealer-get_info) | POST | Get the current dealer's profile information. |
| [`/panel/dealer/activation_code/create`](dealer/activation_code.md#post-panel-dealer-activation_code-create) | POST | Generate device activation codes. |
| [`/panel/dealer/activation_code/list`](dealer/activation_code.md#post-panel-dealer-activation_code-list) | POST | List existing activation codes. |
| [`/panel/dealer/activation_code/update`](dealer/activation_code.md#post-panel-dealer-activation_code-update) | POST | Update the status of activation codes. |
| [`/panel/dealer/password/update`](dealer/password.md#post-panel-dealer-password-update) | POST | Update the dealer's password. |
| [`/panel/dealer/settings/image/upload`](dealer/settings/image.md#post-panel-dealer-settings-image-upload) | POST | Upload a branding image. |
| [`/panel/dealer/settings/image/delete`](dealer/settings/image.md#post-panel-dealer-settings-image-delete) | POST | Delete a branding image. |
| [`/panel/dealer/settings/notification/read`](dealer/settings/notification.md#post-panel-dealer-settings-notification-read) | POST | Read notification settings. |
| [`/panel/dealer/settings/notification/update`](dealer/settings/notification.md#post-panel-dealer-settings-notification-update) | POST | Update notification settings. |
| [`/panel/dealer/settings/service/read`](dealer/settings/service.md#post-panel-dealer-settings-service-read) | POST | Read service settings. |
| [`/panel/dealer/settings/service/update`](dealer/settings/service.md#post-panel-dealer-settings-service-update) | POST | Update service settings. |

### Subpaas

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/subpaas/list`](subpaas/README.md#post-panel-subpaas-list) | POST | List sub-dealers under the current dealer. |
| [`/panel/subpaas/read`](subpaas/README.md#post-panel-subpaas-read) | POST | Read a single sub-dealer. |
| [`/panel/subpaas/create`](subpaas/README.md#post-panel-subpaas-create) | POST | Create a sub-dealer. |
| [`/panel/subpaas/update`](subpaas/README.md#post-panel-subpaas-update) | POST | Update a sub-dealer. |
| [`/panel/subpaas/password/change`](subpaas/password.md#post-panel-subpaas-password-change) | POST | Change a sub-dealer's password. |
| [`/panel/subpaas/session/create`](subpaas/session.md#post-panel-subpaas-session-create) | POST | Open a session as a sub-dealer. |

### User

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/user/list`](user/README.md#post-panel-user-list) | POST | List users. |
| [`/panel/user/read`](user/README.md#post-panel-user-read) | POST | Read a single user. |
| [`/panel/user/export`](user/README.md#post-panel-user-export) | POST | Export users. |
| [`/panel/user/change_password`](user/README.md#post-panel-user-change_password) | POST | Change a user's password. |
| [`/panel/user/corrupt`](user/README.md#post-panel-user-corrupt) | POST | Corrupt a user account, marking it and its data as unusable. |
| [`/panel/user/session/create`](user/README.md#post-panel-user-session-create) | POST | Open a session as a user. |
| [`/panel/user/transaction/list`](user/README.md#post-panel-user-transaction-list) | POST | List a user's balance transactions. |
| [`/panel/user/transaction/change_balance`](user/README.md#post-panel-user-transaction-change_balance) | POST | Change a user's balance. |
| [`/panel/user/create`](user/README.md#post-panel-user-create) | POST | Create a user. |
| [`/panel/user/update`](user/README.md#post-panel-user-update) | POST | Update a user. |
| [`/panel/user/upload`](user/README.md#post-panel-user-upload) | POST | Bulk-create users from a spreadsheet. |
| [`/panel/user/menu/preset/list`](user/preset.md#post-panel-user-menu-preset-list) | POST | List menu presets. |
| [`/panel/user/menu/preset/create`](user/preset.md#post-panel-user-menu-preset-create) | POST | Create a menu preset. |
| [`/panel/user/menu/preset/update`](user/preset.md#post-panel-user-menu-preset-update) | POST | Update a menu preset. |
| [`/panel/user/menu/preset/delete`](user/preset.md#post-panel-user-menu-preset-delete) | POST | Delete a menu preset. |
| [`/panel/user/menu/preset/assign`](user/preset.md#post-panel-user-menu-preset-assign) | POST | Assign a menu preset to a user. |
| [`/panel/user/menu/item/list`](user/preset.md#post-panel-user-menu-item-list) | POST | List available menu items. |
| [`/panel/user/mfa/settings/update`](user/mfa-settings.md#post-panel-user-mfa-settings-update) | POST | Update a user's MFA settings. |
| [`/panel/user/mfa/settings/default/read`](user/mfa-settings.md#post-panel-user-mfa-settings-default-read) | POST | Read the default MFA settings. |
| [`/panel/user/mfa/settings/default/update`](user/mfa-settings.md#post-panel-user-mfa-settings-default-update) | POST | Update the default MFA settings. |

### Tracker

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/tracker/list`](tracker.md#post-panel-tracker-list) | POST | List trackers. |
| [`/panel/tracker/read`](tracker.md#post-panel-tracker-read) | POST | Read a single tracker. |
| [`/panel/tracker/clone`](tracker.md#post-panel-tracker-clone) | POST | Create a clone of a tracker for another user. |
| [`/panel/tracker/batch_clone`](tracker.md#post-panel-tracker-batch_clone) | POST | Clone up to 1,000 trackers to one user in a single request. |
| [`/panel/tracker/delete_clone`](tracker.md#post-panel-tracker-delete_clone) | POST | Delete a tracker clone. |
| [`/panel/tracker/batch_delete_clones`](tracker.md#post-panel-tracker-batch_delete_clones) | POST | Delete up to 500 tracker clones in one request. |
| [`/panel/tracker/corrupt`](tracker.md#post-panel-tracker-corrupt) | POST | Corrupt a tracker's device, with no undo. |
| [`/panel/tracker/move`](tracker.md#post-panel-tracker-move) | POST | Move a tracker to another user. |
| [`/panel/tracker/register_retry`](tracker.md#post-panel-tracker-register_retry) | POST | Retry a tracker's device registration. |
| [`/panel/tracker/settings/update`](tracker.md#post-panel-tracker-settings-update) | POST | Update a tracker's settings. |
| [`/panel/tracker/source/update`](tracker.md#post-panel-tracker-source-update) | POST | Update a tracker's underlying device. |
| [`/panel/tracker/tariff/change`](tracker.md#post-panel-tracker-tariff-change) | POST | Change a tracker's service plan. |
| [`/panel/tracker/console/connect`](tracker.md#post-panel-tracker-console-connect) | POST | Open a console connection to a tracker's device. |
| [`/panel/tracker/active/history/list`](tracker.md#post-panel-tracker-active-history-list) | POST | Report how many trackers were active in each month of a date range. |
| [`/panel/tracker/raw_command/send`](tracker.md#post-panel-tracker-raw_command-send) | POST | Send a raw command to a tracker's device. |

### Tariff

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/tariff/list`](tariff.md#post-panel-tariff-list) | POST | List plans. |
| [`/panel/tariff/read`](tariff.md#post-panel-tariff-read) | POST | Read a single plan. |
| [`/panel/tariff/create`](tariff.md#post-panel-tariff-create) | POST | Create a plan. |
| [`/panel/tariff/update`](tariff.md#post-panel-tariff-update) | POST | Update a plan. |
| [`/panel/tariff/defaults/read`](tariff.md#post-panel-tariff-defaults-read) | POST | Read the per-device-type plan defaults. |
| [`/panel/tariff/defaults/update`](tariff.md#post-panel-tariff-defaults-update) | POST | Update the per-device-type plan defaults. |

### Order

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/order/read`](order.md#post-panel-order-read) | POST | Read an equipment order. |

### Gateways

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/gateways/email/list`](gateways.md#post-panel-gateways-email-list) | POST | List available email gateways. |

### Timezone

| Endpoint | Method | Description |
| --- | --- | --- |
| [`/panel/timezone/list`](timezone.md#post-panel-timezone-list) | POST | List the platform-wide supported time zones. |

## Support

For questions and support, contact the [Navixy developer support team](../../general/contacts.md).
