---
description: >-
  Dashboards API reference: the API operations that list the dashboards,
  read a definition, panel data, and permissions, and their models.
tags:
  - reference
  - beta
---

# Dashboards API reference

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Dashboards in a browser. See [API access](../authentication.md#api-access).
{% endhint %}

This page is the complete reference of the Dashboards API operations: every operation with its request and responses, then the response models.

The base address is `https://dashboards.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. Every request needs the session token of a signed-in browser, as [API access](../authentication.md#api-access) describes.

## Operations

{% openapi-operation spec="analytics-dashboards" path="/dashboards" method="get" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-dashboards" path="/dashboards/{id}" method="get" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-dashboards" path="/dashboards/{id}/panels/{panelId}" method="get" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-dashboards" path="/me/permissions" method="get" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-operation %}

## Response models

{% openapi-schemas spec="analytics-dashboards" schemas="Dashboard" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardDefinition" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardRange" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="PanelDefinition" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="GridPos" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="PanelFieldDefinition" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="PanelData" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Permissions" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardPermissions" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Error" grouped="false" %}
[OpenAPI analytics-dashboards](../reference/Dashboards.json)
{% endopenapi-schemas %}
