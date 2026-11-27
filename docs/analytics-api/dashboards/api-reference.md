---
description: >-
  Dashboards API reference: the operations that list the dashboards and
  periods and get dashboard data, and their response models.
tags:
  - reference
  - beta
---

# Dashboards API reference

This page is the complete reference of the Dashboards API: every operation with its request and responses, then the response models.

The base address is `https://dashboards.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. Every request needs an access token, as [Authentication](../authentication.md) describes.

## API actions

{% openapi-operation spec="analytics-dashboards" path="/tools/context" method="post" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-dashboards" path="/tools/read_dashboard" method="post" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-operation %}

## Response models

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardList" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardPeriods" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardData" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Panel" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="PanelField" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Series" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="ValueSummary" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Error" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}
