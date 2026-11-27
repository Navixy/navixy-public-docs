---
description: >-
  Dashboards tools reference: the tool operations that list the dashboards
  and periods and get dashboard data with an API key, and their models.
tags:
  - reference
  - beta
---

# Dashboards tools

{% hint style="info" %}
**Tools, with an API key**

The operations on this page are tools. They accept the access token that your app gets by exchanging a Navixy Console API key. See [Tools access](../../authentication.md#tools-access).
{% endhint %}

This page is the complete reference of the Dashboards tools: every tool operation with its request and responses, then the response models. The tools are for integrations and AI agents. To learn how to use them, see [Dashboard data](dashboard-data.md).

The base address is `https://dashboards.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`. Every request needs an access token, as [Authentication](../../authentication.md#tools-access) describes.

## List the dashboards and periods

The following request lists the dashboards and periods that the token can read, in the time zone of Berlin:

```bash
curl --request POST \
  --url 'https://dashboards.<REGION>.navixy.com/api/tools/context' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Berlin"}'
```

Response:

```json
{
  "now": "2026-11-27T16:07:52+01:00",
  "timeZone": "Europe/Berlin",
  "dashboards": [
    { "code": "live_status", "name": "Live status", "ranges": [] },
    { "code": "trips", "name": "Trips", "ranges": ["1h", "6h", "12h", "24h", "7d", "14d", "31d"] },
    { "code": "conditions", "name": "Conditions", "ranges": ["1h", "6h", "12h", "24h", "7d", "14d", "31d"] }
  ]
}
```

`now` is the current time in the time zone of the request, to the second. `dashboards` lists the dashboards that the token's role grants, in the order of the tabs in Navixy Dashboards. The codes and the period IDs are the values that a dashboard data request accepts. Live status has an empty `ranges` list, because it has no periods. When the role grants the `dashboards-standard` feature but no dashboard, `dashboards` is an empty list.

This request reads no panel data, so it doesn't count toward the [daily limit](../../limits.md#tools-limits) of dashboard reads.

## Tools

{% openapi-operation spec="analytics-dashboards" path="/tools/context" method="post" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-operation %}

{% openapi-operation spec="analytics-dashboards" path="/tools/read_dashboard" method="post" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-operation %}

## Response models

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardList" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardPeriods" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="DashboardData" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Panel" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="PanelField" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Series" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="ValueSummary" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}

{% openapi-schemas spec="analytics-dashboards" schemas="Error" grouped="false" %}
[OpenAPI analytics-dashboards](../../reference/Dashboards.json)
{% endopenapi-schemas %}
