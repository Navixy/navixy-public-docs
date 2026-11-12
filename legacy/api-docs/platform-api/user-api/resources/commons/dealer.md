---
title: Dealer
description: The interface configuration of the telematics service provider (TSP) and the platform-wide limits that constrain other operations.
---

# Dealer

This resource describes the telematics service provider (TSP) whose platform an account belongs to. In the API, a TSP account is called a dealer, so its methods and fields use the word `dealer`. This operation returns the TSP's interface configuration, the branding features it has enabled, and the platform-wide limits that other operations enforce.

The `platform` block is the part integrations care about most, because it states limits that are otherwise only discoverable by hitting them: `history.max_limit` caps the [history](history/README.md) list operations, `report.max_time_span` caps [report generation](report/report_tracker.md#post-report-tracker-generate), and `stats.max_time_span` and `stats.max_allowed_trackers` cap the [stats operations](../tracking/tracker/stats/stats_mileage.md). Date and time values follow the [platform formats](../../../general/api-conventions.md#date-time-formats).

### Dealer features

| Name | Description |
| ---- | ----------- |
| `branding_web` | Allows custom logos, colour theme, domain, and favicon in the web interface. |
| `branding_mobile` | Allows a custom icon, logo, and colour theme in the mobile applications. |
| `subpaas` | Allows resellers. Can be used only together with `navixy_label`. |
| `navixy_label` | Shows "Powered by Navixy" in the interface. Required for the `subpaas` feature. |

## API actions

API base path: `/dealer`.

***

{% openapi-operation spec="navixy-platform" path="/dealer/get_ui_config" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 12 - Dealer not found, when no matching TSP account is in the database.
* 201 - Not found in the database, when the TSP account has no interface settings data.
