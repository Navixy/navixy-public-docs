---
title: Tracker
description: API calls to interact with trackers in the admin panel.
---

# Tracker

API calls to manage GPS tracking devices within the Admin Panel.

A **tracker** is a device as presented to one owning user. A **clone** is a second tracker sharing the same underlying device, so two users can see the same device independently. The **source** is that underlying device, and several operations act on it rather than on the tracker.

## Tracker object

{% openapi-schemas spec="admin-panel" schemas="Tracker,TrackerSource" grouped="true" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-schemas %}

`source.device_id` is the identifier required by the raw command operation, which is the one operation on this page that does not take `tracker_id`.

## API actions

API path: `panel/tracker`.

***

_required permissions_: `trackers: "report"`.

{% hint style="info" %}
This operation needs the `report` operation on `trackers`, which is distinct from `read`. A dealer granted only `read` receives error code 13.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/tracker/active/history/list" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

`from` and `to` are **months**, written `YYYY-MM`. A full date and time is rejected. This differs from [user/transaction/list](user/README.md#post-panel-user-transaction-list), which takes the same two parameter names with full `YYYY-MM-DD HH:MM:SS` values.

***

_required permissions_: `trackers: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/clone" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 219 - Not allowed for clones of the device - when the source tracker is itself a clone.
* 201 - Not found in the database - when the specified `tracker_id` is not found.
* 246 - Invalid user ID - when the user ID matches the owner of the source tracker, or does not exist or belong to the authorized dealer.
* 247 - Entity already exists - if the destination user already has a clone of this tracker.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/console/connect" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 230 - Not supported for this entity type - when the tracker is deleted or blocked.
* 201 - Not found in the database - when no tracker has that `device_id`.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "corrupt"`.

{% hint style="danger" %}
Corrupting a tracker cannot be undone.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/tracker/corrupt" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if the tracker is not found.
* 219 - Not allowed for clones of the device - if the source tracker is itself a clone.
* 252 - Device already corrupted.
* 253 - Device has clones and `corrupt_clones` is false.

***

_required permissions_: `trackers: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/batch_clone" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 7 - Invalid parameters. Size must be between 1 and 1000 - when the request exceeds 1000 trackers.

***

_required permissions_: `trackers: "delete"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/batch_delete_clones" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

The limit here is **500**, not the 1000 accepted by batch cloning.

***

_required permissions_: `trackers: "delete"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/delete_clone" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if the tracker is not found.
* 249 - Operation available for clones only - if the specified tracker is not a clone.
* 203 - Delete entity associated with - if rules or vehicles are associated with the tracker.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/list" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `trackers: "create"` and `trackers: "delete"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/move" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

{% hint style="warning" %}
The `id` in the response is the **new** tracker ID. Moving a tracker to another user creates a new tracker record, so the `tracker_id` you sent no longer refers to the moved tracker. Store the returned `id` for any subsequent calls about it.
{% endhint %}

#### Errors

* 219 - Not allowed for clones of the device - when the source tracker is a clone.
* 201 - Not found in the database - when the tracker is not found.
* 246 - Invalid user ID - when `user_id` matches the current owner, or does not exist or belong to the authorized dealer.
* 247 - Entity already exists - when the destination user already has a clone of this tracker.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/read" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - when the tracker is not found.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/register_retry" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - when the tracker is not found.
* 252 - Device already corrupted - if the tracker is corrupted.
* 264 - Timeout not reached - if another retry for this tracker was made in the last 120 seconds.
* 208 - Device blocked - when the tracker exists but was blocked due to plan restrictions or another reason.
* 219 - Not allowed for clones of the device - when the specified tracker is a clone.
* 214 - Requested operation or parameters are not supported by the device - when the device has no GSM module.

***

_required permissions_: `trackers: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/settings/update" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

The settings sit at the top level of the request alongside `tracker_id`, not nested under a wrapper.

#### Errors

* 201 - Not found in the database - when the tracker is not found.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "update"`.

{% hint style="warning" %}
Holding `trackers: "update"` may not be enough for this operation. A session with the full `trackers` permission set was refused with error code 13, while every other `trackers: "update"` operation on the same session succeeded. If you receive code 13 here despite holding the permission, contact support.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/tracker/source/update" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - when the tracker is not found.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "update"` and `transactions: "create"`.

{% hint style="warning" %}
This operation has billing consequences and can create a transaction against the balance of the user.
{% endhint %}

{% openapi-operation spec="admin-panel" path="/panel/tracker/tariff/change" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 221 - Device limit exceeded - when the device limit of the new plan is lower than the tracker count of the user.
* 252 - Device already corrupted - when the source of the tracker is corrupted.

***

_required permissions_: `trackers: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/tracker/raw_command/send" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

This is the only operation on this page that identifies the device by `device_id` rather than `tracker_id`. Take the value from `source.device_id` on the tracker.

#### Errors

* 7 - Invalid parameters.
* 201 - Not found in the database - if no tracker with that device ID belongs to the authorized dealer.
* 252 - Device already corrupted - if the source of the tracker is corrupted.
