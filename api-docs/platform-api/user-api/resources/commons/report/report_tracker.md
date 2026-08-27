---
title: Report tracker
description: Generate, poll, retrieve, and download the account's tracker reports.
---

# Report tracker

A report turns a period of tracking data into tables and graphs: distance travelled, stops made, fuel used, geofences visited. Which report is produced is decided by the plugin named in `plugin.plugin_id`, and most of the `plugin` object is specific to that plugin.

Generation is asynchronous, so a caller runs a sequence rather than a single call: `generate` queues the report and returns its ID, `status` reports progress, and `retrieve` or `download` fetches the result. For a walkthrough, see [how to obtain report information](../../../guides/data-retrieval/obtain-reports.md).

## API actions

API base path: `/report/tracker`.

Every operation on this page requires the `reports` sub-user right.

The `geocoder` parameter chooses which [geocoder](../../tracking/geocoder.md) resolves addresses. The plugin-specific parameters are documented in [report plugins](../plugin/report_plugins.md). A plugin object looks like this:

```json
{
  "details_interval_seconds": 300,
  "plugin_id": 9,
  "show_seconds": false,
  "graph_type": "time",
  "smoothing": false,
  "sensors": [
    {
      "tracker_id": 123456,
      "sensor_id": 123456
    }
  ]
}
```

{% hint style="info" %}
`include_places_in_address` and `include_zones_in_address` both default to `true`, preserving the historical behaviour of prefixing an address with the name of the place or geofence containing the point. They apply to report plugins that render an address column, such as trips or stops. The values are echoed back in [`list`](#post-report-tracker-list) under `parameters`, and are stored with [scheduled reports](report_schedule.md).
{% endhint %}

***

{% openapi-operation spec="navixy-platform" path="/report/tracker/generate" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 15 - Too many requests. One user can only have so many reports generating in parallel.
* 211 - Requested time span is too big, when the interval from `from` to `to` exceeds the maximum allowed.
* 217 - List contains nonexistent entities, when a tracker ID belongs to no tracker or to another user's.
* 222 - Plugin not found, when no report plugin has the given ID.
* 236 - Feature unavailable due to tariff restrictions, when one of the trackers is on a tariff with reports disabled, meaning `has_reports` is false.

Error 211 carries the maximum allowed period in the response body, so a caller can retry with a period that fits:

```json
{
  "success": false,
  "status": {
    "code": 211,
    "description": "Requested time span is too big"
  },
  "max_time_span": "120 days"
}
```

`max_time_span` is a human-readable English phrase such as `120 days` or `26 hours`. It is **not** an ISO 8601 duration, so parse it as text rather than as a duration literal.

***

{% openapi-operation spec="navixy-platform" path="/report/tracker/status" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - Entity not found, when no report has the given ID.

***

{% openapi-operation spec="navixy-platform" path="/report/tracker/retrieve" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - Entity not found, when no report has the given ID.
* 229 - Requested data is not ready yet, when the report exists but is still generating.

***

{% openapi-operation spec="navixy-platform" path="/report/tracker/download" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 204 - Entity not found, when no report has the given ID.
* 229 - Requested data is not ready yet, when the report exists but is still generating.

***

{% openapi-operation spec="navixy-platform" path="/report/tracker/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

{% openapi-operation spec="navixy-platform" path="/report/tracker/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 101 - In demo mode this function is disabled.
