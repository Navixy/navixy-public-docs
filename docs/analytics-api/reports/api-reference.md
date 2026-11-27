---
description: >-
  The complete reference of the Navixy Reports API: every operation with its
  parameters and responses, and the main response models.
tags:
  - reference
  - beta
---

# Reports API reference

This page is the complete reference of the Reports API: every operation with its request and responses, then the response models.

The base address is `https://reports.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. Every request needs an access token, as [Authentication](../authentication.md) describes.

## API actions

{% openapi-operation spec="analytics-reports" path="/tools/context" method="post" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/tools/run_report_totals" method="post" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-reports" path="/tools/propose_report" method="post" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-operation %}

## Response models

{% openapi-schemas spec="analytics-reports" schemas="ReportTypesAndAssets" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Period" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ReportTypeInfo" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="AssetInfo" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TripsSummary" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="TripFigures" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="AssetTrips" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorSummary" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="SensorLine" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="ReportLink" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="View" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-reports" schemas="Error" grouped="false" %}
[OpenAPI analytics-reports](../reference/Reports.json)
{% endopenapi-schemas %}
