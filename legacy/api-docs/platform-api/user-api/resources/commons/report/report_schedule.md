---
title: Report schedule
description: Deliver a report to a user's mailbox on a repeating schedule.
---

# Report schedule

A report schedule runs a [report](report_tracker.md) on a repeating basis and emails the result, so a weekly mileage summary arrives without anyone asking for it.

A schedule holds the same report definition that [`report/tracker/generate`](report_tracker.md#post-report-tracker-generate) takes, plus when to run it and where to send it.

## Schedule entry object

{% openapi-schemas spec="navixy-platform" schemas="ReportScheduleEntry" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The `report` block takes the same parameters as [`report/tracker/generate`](report_tracker.md#post-report-tracker-generate), including the optional `include_places_in_address` and `include_zones_in_address` flags. Both are boolean and default to `true`, and they decide whether place and geofence names are prefixed to addresses in the generated report.

## API actions

API base path: `/report/schedule`.

Every operation on this page requires the `reports` sub-user right.

***

{% openapi-operation spec="navixy-platform" path="/report/schedule/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - List contains nonexistent entities, when a tracker ID belongs to no tracker or to another user's.
* 222 - Plugin not found, when no report plugin has the given ID.
* 236 - Feature unavailable due to tariff restrictions.

***

{% openapi-operation spec="navixy-platform" path="/report/schedule/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 217 - List contains nonexistent entities, when a tracker ID belongs to no tracker or to another user's.
* 222 - Plugin not found, when no report plugin has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when a device's tariff does not allow reports.

***

{% openapi-operation spec="navixy-platform" path="/report/schedule/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/report/schedule/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when no schedule has the given ID.
