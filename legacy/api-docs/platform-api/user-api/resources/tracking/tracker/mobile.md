---
title: Mobile app register
description: Deprecated. Register a mobile application as a tracker.
---

# Mobile app register

{% hint style="warning" %}
**Deprecated.** This action should not be used. Register a mobile application with [`tracker/register`](./#post-tracker-register) and `plugin_id` 35 instead.
{% endhint %}

This operation registered a phone running the mobile application as a tracker in its own right. It still answers, and is documented here because it is still reachable, but `tracker/register` covers the same ground and is maintained.

As with `tracker/register`, the chosen [registration plugin](../../commons/plugin/README.md) adds its own parameters on top of the ones the operation lists. The group must already exist. List groups with [`group/list`](group.md#post-tracker-group-list). Models with a fixed device ID are listed by [`tracker/list_models`](./#post-tracker-list_models). The call returns a [tracker object](README.md#tracker-object-structure).

## API actions

API base path: `/tracker/mobile`.

***

_Required sub-user rights:_ `tracker_register`.

{% openapi-operation spec="navixy-platform" path="/tracker/mobile/register" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The user lacks the right.
* 204 - The group does not exist.
* 221 - The dealer's device limit is exceeded.
* 224 - The device ID is already registered.
* 225 - The tariff is not compatible with the account's legal type.
