---
title: Sub-user security group
description: Named sets of rights assigned to sub-users, deciding what each of them may change.
---

# Sub-user security group

A security group is a named set of rights. Assigning one to a [sub-user](README.md) decides what that sub-user may change, which is the half of their limits that scoping does not cover: scoping decides what they can see, rights decide what they can do to it.

## Security group object

{% openapi-schemas spec="navixy-platform" schemas="SecurityGroup" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

### Default security group

The default, or empty, security group applies when a sub-user's `security_group_id` is null. Its `rights` array is empty, so such a sub-user can change nothing.

### Master user's rights

A master user always holds every right, including the `admin` right, which cannot be assigned to a security group.

### Security group rights

Almost every read operation requires no rights at all, so it is available to any sub-user, even one in the default group. Entities can still be hidden from such a sub-user, because they are associated with trackers outside that sub-user's scope. Most operations that change data, by contrast, do require a right.

These are the rights a security group can grant:

| name                          | description                                                                                                                                                                                                                                                                                                                                                  |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `admin`                       | Available for master user-only. Cannot be assigned to security groups.                                                                                                                                                                                                                                                                                       |
| `tracker_update`              | Allows adjustments to platform-related tracker settings, including labeling, tagging, changing phone numbers, LBS location settings, parking detection settings, odometer settings, engine hours settings, working statuses, data forwarding, connection timeout settings, inputs and sensors management, and BLE sensors management for some device models. |
| `tracker_configure`           | Allows adjustments to hardware-related tracker settings that require sending device configuration commands. This includes tracking mode settings, ignition input settings, timezone settings, harsh driving settings, etc.                                                                                                                                   |
| `tracker_set_output`          | Allows changing the output state.                                                                                                                                                                                                                                                                                                                            |
| `tracker_register`            | Allows activating new trackers.                                                                                                                                                                                                                                                                                                                              |
| `tracker_rule_update`         | Allows creating and updating rules.                                                                                                                                                                                                                                                                                                                          |
| `tag_update`                  | Allows creating and updating tags.                                                                                                                                                                                                                                                                                                                           |
| `task_update`                 | Allows creating and updating tasks.                                                                                                                                                                                                                                                                                                                          |
| `form_template_update`        | Allows creating and updating forms.                                                                                                                                                                                                                                                                                                                          |
| `zone_update`                 | Allows creating and updating geofences.                                                                                                                                                                                                                                                                                                                      |
| `place_update`                | Allows creating and updating places.                                                                                                                                                                                                                                                                                                                         |
| `places_custom_fields_update` | Allows creating and updating custom fields for places.                                                                                                                                                                                                                                                                                                       |
| `employee_update`             | Allows creating and updating employees and drivers.                                                                                                                                                                                                                                                                                                          |
| `vehicle_update`              | Allows creating and updating vehicles, garages, and adding avatars to vehicles.                                                                                                                                                                                                                                                                              |
| `video_monitoring`            | Allows requesting real-time video, playback video, and video events.                                                                                                                                                                                                                                                                                         |
| `payment_create`              | Allows interacting with the payment system assigned to a user.                                                                                                                                                                                                                                                                                               |
| `reports`                     | Allows generating all types of reports.                                                                                                                                                                                                                                                                                                                      |
| `weblocator_session_create`   | Allows creating geo-links.                                                                                                                                                                                                                                                                                                                                   |
| `delivery_session_create`     | Allows using the delivery tracking functionality.                                                                                                                                                                                                                                                                                                            |
| `checkin_update`              | Allows creating check-ins.                                                                                                                                                                                                                                                                                                                                   |

## API actions

API base path: `/subuser/security_group`.

Every operation on this page needs the `multilevel_access` tariff feature on **all** trackers, and the `admin` right, which is available only to master users. Failing either returns error 13 for the right and error 236 for the feature, so those two are not repeated per operation below.

***

{% openapi-operation spec="navixy-platform" path="/subuser/security_group/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/security_group/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no group has the given `security_group_id`.

***

{% openapi-operation spec="navixy-platform" path="/subuser/security_group/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 13 - The caller is a sub-user.
* 236 - The account lacks the `multilevel_access` feature.

***

{% openapi-operation spec="navixy-platform" path="/subuser/security_group/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the group does not exist.

***

{% openapi-operation spec="navixy-platform" path="/subuser/security_group/assign" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when the group or the sub-user does not exist.
