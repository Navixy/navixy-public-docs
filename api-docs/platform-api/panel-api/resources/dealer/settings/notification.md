---
title: Notification
description: API calls to read and update notification settings.
---

# Notification Settings

Notification settings in the Navixy Admin Panel are used to manage and configure alerts and notifications for various events and conditions. These settings allow you to customize notification channels, specifying how notifications are delivered, including options like email, SMS, or push notifications.

## Notification Settings Object

{% openapi-schemas spec="admin-panel" schemas="NotificationSettings" grouped="true" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

`email_special` is the same underlying value as `email_special` in the [service settings](service.md#service-settings-object). Writing it through either endpoint is visible in the other.

## API actions

API path: `panel/dealer/settings/notification`.

***

_required permissions_: `notification_settings: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/settings/notification/read" method="post" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

[General](../../../../general/errors.md#error-codes) types only.

***

_required permissions_: `notification_settings: "update"`.

{% hint style="warning" %}
This is a full replacement, not a partial update. Any writable field you omit is reset to its default rather than left alone: omitting `email_special` clears it. Read the current settings first, change what you need, and send the complete object back.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/dealer/settings/notification/update" method="post" %}
[OpenAPI admin-panel](../../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

[General](../../../../general/errors.md#error-codes) types only.

Omitting any of `email_from`, `email_footer`, `sms_originator`, or `caller_id` returns error code 7 with an `errors` array naming the missing field.
