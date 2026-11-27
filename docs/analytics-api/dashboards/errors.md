---
description: >-
  The errors that only Navixy Dashboards returns, for the API operations and
  for the tools, and how to handle them.
tags:
  - reference
  - beta
---

# Dashboards errors

{% hint style="info" %}
**API and tools**

This page covers both approaches: the API operations, with a session token, and the tools, with a Navixy Console API key.
{% endhint %}

Dashboards uses the error format and the error codes of [Error handling](../error-handling.md#common-error-codes). This page describes what only Dashboards returns, for each approach. It's for developers who handle these errors in their app.

## A dashboard that the role doesn't grant

The 2 approaches refuse a dashboard that the role doesn't grant in different ways:

* The API returns HTTP 403 with `insufficient_rights`, whether the dashboard exists or not.
* The tools leave the dashboard out of [List dashboards and periods](tools/README.md). A request for its data returns HTTP 400 with `invalid_tool_call`, and `details.dashboard` lists the codes that the token can read, or is `this user may open no dashboard`.

Ask your organization's administrator for a role that grants the dashboards that your app needs.

## API errors

The API operations return these codes of their own:

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `range_not_found` | The dashboard has no period with the `range` value, or it's Live status, which has no periods. | Use a period `id` from the dashboard's `ranges`, or leave out `range`. |
| 400 | `invalid_panel_request` | `previous` is neither `true` nor `false`, or it's `true` without `range` or on Live status. `details.previous` says which. | Send `previous=true` only together with `range`, on Trips or Conditions. |
| 401 | None | The request has no session token, or the token is invalid or expired. The access token of an API key gets 401 here too, because the API operations accept only a session token. | Sign in again. For an API key, use the [tools](tools/README.md). |
| 403 | `insufficient_rights` | The role gives access to Dashboards, but lacks its `dashboards-standard` feature or doesn't grant this dashboard. | Ask your organization's administrator for a role that includes the feature and the dashboard. |
| 403 | `token_incomplete` | Only on [Read panel data](api-reference.md): the token's organization or workspace is missing or isn't a valid ID. `details.claim` names which. | Sign in again. If the error repeats, contact Navixy support. |
| 404 | `dashboard_not_found` | No dashboard has this ID. Only a role that grants every dashboard gets this code: other roles get `insufficient_rights`, whether the dashboard exists or not. | Use an `id` from [List dashboards](api-reference.md). |
| 404 | `panel_not_found` | The dashboard has no panel with this ID. | Use a panel `id` from the dashboard's definition. |
| 503 | `panel_data_unavailable` | The panel's data couldn't be read: the query took too long, used too much memory, or returned more than 10,000 rows, or the data store returned no response. A slow panel can also end with an HTTP 5xx error without a JSON body. | Repeat the request later, or read a shorter period. See [How to retry a request](../error-handling.md#how-to-retry-a-request). |

The API checks access first. So a session without access gets HTTP 403 whatever the request has. Only a session with access gets the code of a wrong panel ID or period.

The following response is the error of a panel request with `previous=true` and no `range`:

```json
{
  "code": "invalid_panel_request",
  "message": "This panel's data cannot be read as asked.",
  "details": {
    "previous": "only with range: the period it is the one before"
  }
}
```

### How to handle a wrong panel or period

Take the panel IDs and the period IDs from the dashboard's definition, as [Dashboard definitions](dashboard-definitions.md#read-the-definition-of-a-dashboard) describes. Send `range` only for Trips and Conditions, and `previous` only with `range`. Don't repeat the same request, because it returns the same error.

## Tools errors

### Error codes of the tools

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 403 | `insufficient_rights` | The role gives access to Dashboards, but lacks its `dashboards-standard` feature, which both tool operations need. | Ask your organization's administrator for a role that includes the `dashboards-standard` feature. |

For `tool_unavailable` and the other codes that every tool returns, see [Tools error codes](../error-handling.md#tools-error-codes). In Dashboards, `tool_unavailable` means that Navixy is reading as many dashboards as it can, or that a request for dashboard data took too long. A request that took too long counts toward the daily limit, and one refused because Navixy is busy doesn't. See [Tools limits](../limits.md#tools-limits).

### Details of a wrong field

When a field of the body is wrong, the response is HTTP 400 with `invalid_tool_call`, and `details` names the field. The tools write these texts:

<table><thead><tr><th width="140">Field in details</th><th width="300">When it occurs</th><th>Text</th></tr></thead><tbody><tr><td><code>timeZone</code></td><td>The field is missing, isn't an IANA time zone name, or is a fixed offset other than <code>UTC</code>.</td><td><code>the user's IANA time zone, such as Europe/Madrid, not an offset</code></td></tr><tr><td><code>body</code></td><td>The body is empty, or it's JSON but not an object.</td><td><code>a JSON object of the tool's arguments</code></td></tr><tr><td><code>dashboard</code></td><td>The field is missing.</td><td><code>required</code></td></tr><tr><td><code>dashboard</code></td><td>The token can't read this dashboard.</td><td>The codes that the token can read, such as <code>one of live_status, trips, conditions</code>, or <code>this user may open no dashboard</code></td></tr><tr><td><code>range</code></td><td>Trips or Conditions without a period, or with a period that the dashboard doesn't have.</td><td><code>one of 1h, 6h, 12h, 24h, 7d, 14d, 31d</code>, after <code>required: </code> when the field is missing</td></tr><tr><td><code>range</code></td><td>Any period on Live status.</td><td><code>live_status has no periods: leave it out</code></td></tr><tr><td><code>previous</code></td><td>The value isn't a JSON boolean.</td><td><code>true or false</code></td></tr><tr><td><code>previous</code></td><td><code>true</code> on Live status.</td><td><code>live_status has no periods, so none before</code></td></tr><tr><td>Any other name</td><td>The body has a field that the operation doesn't accept, such as <code>workspace_id</code>. The workspace is always the token's.</td><td><code>not a parameter of this tool</code></td></tr></tbody></table>

A wrong `timeZone`, or a body that isn't an object, is reported alone, before Navixy checks the other fields. The texts can change, but the field names in `details` are the names of the body's fields.

The following response is the error of a request for the data of Trips with `"range": "2d"`, a period that Trips doesn't have:

```json
{
  "code": "invalid_tool_call",
  "message": "The tool call cannot be run as it is.",
  "details": {
    "range": "one of 1h, 6h, 12h, 24h, 7d, 14d, 31d"
  }
}
```

#### How to handle a wrong dashboard or period

Take the dashboard codes and the period IDs from the response of [List dashboards and periods](tools/README.md#list-the-dashboards-and-periods). Send `range` only for Trips and Conditions. For the general steps, see [How to handle a wrong field](../error-handling.md#how-to-handle-a-wrong-field).

## See also

* [Error handling](../error-handling.md#common-error-codes): The error format and the error codes of both products.
* [Limits](../limits.md): The limits of the API and of the tools.
* [Panel data](panel-data.md): The parameters that `range_not_found` and `invalid_panel_request` describe.
* [Dashboard data](tools/dashboard-data.md): The body fields that `details` names in the tools.
