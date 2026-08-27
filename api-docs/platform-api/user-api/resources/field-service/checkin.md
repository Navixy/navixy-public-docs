---
title: Check-ins
description: What a field employee reported from site, with a comment, a photo, and a filled form.
---

# Check-ins

A check-in is what a field employee sends from where they are standing: the time, the address and coordinates, and whatever they added, which can be a comment, a photo, or a filled [form](form/README.md). It is how someone on site tells the office what they found without a phone call, whether that is photographic proof of work done or a report of a fault.

Check-ins come from the Mobile Tracker App, for [Android](https://play.google.com/store/apps/details?id=com.navixy.xgps.tracker\&hl=ru) and [iOS](https://apps.apple.com/us/app/x-gps-tracker/id802887190), rather than from this API. [`checkin/create`](checkin.md#post-checkin-create) exists for exceptional cases only and is described in [how to work with check-ins](../../guides/field-service-management/check-ins.md), so treat this resource as read-mostly.

## Check-in object

{% openapi-schemas spec="navixy-platform" schemas="Checkin" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/checkin`.

The `conditions` parameter of `checkin/list` takes [search conditions](../commons/entity/search_conditions.md).

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/checkin/read" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 204 - Entity not found.

***

_Required sub-user rights:_ `employee_update`.

{% openapi-operation spec="navixy-platform" path="/checkin/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 211 - Requested time span is too big.
* 217 - The list contains non-existent entities - if one of the specified trackers does not exist, is blocked or doesn't have required tariff features.
* 221 - Device limit exceeded - if device limit set for the user's dealer has been exceeded.

***

_Required sub-user rights:_ `checkin_update`.

{% openapi-operation spec="navixy-platform" path="/checkin/delete" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 201 - Not found in the database - check-ins with the specified IDs don't exist, or their corresponding trackers are not available to current sub-user.

***

_Required sub-user rights:_ `checkin_update`.

{% openapi-operation spec="navixy-platform" path="/checkin/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters.
* 201 - Not found in the database - form with the specified IDs don't exist, or their corresponding trackers are not available to current sub-user.
* 242 - There were errors during content validation, if given values are invalid for the form.

***

{% openapi-operation spec="navixy-platform" path="/checkin/image/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 268 - File cannot be created due to quota violation.
* 271 - File size is larger than the maximum allowed (by default 16 MB).

### How to upload file data

The upload target returned by `checkin/image/create` and `checkin/form/file/create` is used the same way in both cases. POST the file as `multipart/form-data` to the returned `url`, sending every entry of `fields` first and the file part last, named as `file_field_name` says.

With internal storage `fields` carries a single `token`. With Amazon S3 it carries `policy`, `key`, `success_action_status`, `x-amz-algorithm`, `x-amz-credential`, `x-amz-date`, `x-amz-signature` and `x-amz-server-side-encryption`, and each must be sent as its own form-data part before the file.

***

{% openapi-operation spec="navixy-platform" path="/checkin/form/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no template with such an ID.

***

{% openapi-operation spec="navixy-platform" path="/checkin/form/file/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no check-in with such an ID, or check-in doesn't have form, or form has no field with such a field\_id.
* 231 - Entity type mismatch - if form field is not file-based, i.e. doesn't use file ID as its value.
* 267 - Too many entities - if there are 6 or more unsubmitted files already associated with this form's field.
* 268 - File cannot be created due to quota violation.
* 271 - File size is larger than the maximum allowed (by default 16 MB).

