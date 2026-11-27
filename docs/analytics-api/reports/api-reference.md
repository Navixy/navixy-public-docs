---
description: >-
  The complete reference of the API of Navixy Reports: every operation with its
  request and responses, and the response models.
tags:
  - reference
  - beta
---

# Reports API reference

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Reports in a browser. See [API access](../authentication.md#api-access).
{% endhint %}

This page is the complete reference of the Reports API: every operation with its request and responses, then the response models.

The base address is `https://reports.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. Every request needs the session token of a signed-in browser, as [API access](../authentication.md#api-access) describes.

## Operations

{% openapi-operation spec="analytics-reports" path="/reports" method="get" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/reports/{id}" method="get" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/reports/{id}/run" method="post" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/reports/{id}/trips/{tripId}/track" method="get" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/assets" method="get" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/me/permissions" method="get" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

## Response models

{% openapi-schemas spec="analytics-reports" schemas="ReportType" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Actions" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="RunRequest" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TripsAndStopsResult" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Trip" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Stop" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorReadingsResult" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorSeries" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorPoint" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="DriverPerformanceResult" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SegmentEvaluation" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ViolationKind" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ViolationEpisode" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="DataFrom" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="DataUntil" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TripTrack" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TrackPoint" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Asset" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Permissions" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ReportTypePermissions" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Error" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}
