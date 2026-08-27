---
title: Driver journal entry
description: A trip that has been categorised, so transport use can be split between business and private.
---

# Driver journal entry

A driver journal entry is a trip that has been categorised. The journal is how a fleet accounts for how its vehicles were used, most often by separating business travel from private, which is what a tax authority or an employer wants to see.

Entries are created from [trip proposals](proposal.md), which are the raw trips the Navixy platform has already detected over a period. For a walkthrough, see [how to use driver journals](../../../guides/fleet-management/driver-journals.md).

## Driver journal entry object

{% openapi-schemas spec="navixy-platform" schemas="DriverJournalEntry" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/driver/journal/entry`.

All these calls need the `driver_journal` feature on the tracker's plan.

There are two ways to get entries: by their IDs, or by specifying a date range. Without `entry_ids`, entries are selected by intersecting their own date range with the range in the request.

***

{% openapi-operation spec="navixy-platform" path="/driver/journal/entry/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 236 - The tracker's plan does not include the driver journal feature.

***

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/driver/journal/entry/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - if a tracker or an employee not found, 242 - if a start date is not before its end date, 247 - if an entry overlaps another.

***

Only two fields, `type` and `comment`, are available to update. Every other field of an entry is rejected rather than ignored.

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/driver/journal/entry/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - if tracker not found, 204 - if entry not found.

***

_Required sub-user rights:_ `vehicle_update`.

{% openapi-operation spec="navixy-platform" path="/driver/journal/entry/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - if any of the given IDs does not exist, in which case nothing is deleted.

***

The response is a driver journal report file, a standard file download, rather than a JSON envelope.

{% openapi-operation spec="navixy-platform" path="/driver/journal/entry/download" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 236 - The tracker's plan does not include the driver journal feature.
