---
title: Commands
description: Reusable commands defined on a tracker, sent to the device or to a URL on demand.
---

# Commands

A command is a reusable action defined on one tracker and run when someone asks for it. Two kinds exist. A **hardware** command sends a protocol string straight to the device, to reboot its firmware or toggle a relay. An **HTTP** command sends a POST request with a JSON body to any URL, optionally carrying live device values in the payload.

Commands are stored per tracker and executed from the [Commands block](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/devices-and-settings/object-management/commands) in the interface or through `command/execute`.

{% hint style="info" %}
Commands are for manual, on-demand actions on a single tracker. For automated, rule-based sending across many devices, use [IoT Logic](https://app.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/s/tx3J5BxnWyPV0nP2xr0z/) with the **Device action** or **Webhook** nodes.
{% endhint %}

{% hint style="warning" %}
Hardware command strings are device-specific. Always check the device manufacturer's documentation for valid values, because an incorrect string can have unintended effects on the device.
{% endhint %}

## Command object

The `config` object takes one of two shapes, chosen by `type`:

{% openapi-schemas spec="navixy-platform" schemas="TrackerCommand" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

In an HTTP command's `body`, `{{attribute_name}}` placeholders are replaced with the device's current values at the moment the command runs.

## Dynamic command values

A hardware command's `command` string, or an HTTP command's `body`, can carry a single `<>` placeholder in place of a fixed value. The caller supplies the substitution in `param` when running the command, and the Navixy platform replaces `<>` before sending. This lets one stored command cover a family of calls, such as a relay command whose channel number changes per use.

It is a different mechanism from the `{{attribute_name}}` placeholders above: those read live device values, while `<>` takes a value from the caller.

* A configuration may contain **at most one** `<>` placeholder. Create and update reject one with more.
* `param` is **required when the configuration contains a placeholder, and rejected when it does not.** Either mismatch returns error 7. A blank value counts as absent.
* `param` accepts up to 500 printable characters.

## API actions

API base path: `/tracker/command`. One call, `batch_get_commands`, sits at the tracker root instead.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/command/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - The command or the tracker does not exist.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/command/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - The command or the tracker does not exist.

***

_Required sub-user rights:_ `tracker_configure`, `tracker_set_output`.

If the command's configuration contains a `<>` placeholder, pass its value in `param`. See [Dynamic command values](commands.md#dynamic-command-values).

{% openapi-operation spec="navixy-platform" path="/tracker/command/execute" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - The command or the tracker does not exist.

***

_Required sub-user rights:_ `tracker_update`.

{% openapi-operation spec="navixy-platform" path="/tracker/command/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - The command or the tracker does not exist.

***

{% openapi-operation spec="navixy-platform" path="/tracker/batch_get_commands" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
