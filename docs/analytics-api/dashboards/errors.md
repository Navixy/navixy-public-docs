---
description: >-
  The errors that only the Navixy Dashboards API returns: its error codes,
  the details of a wrong field, and a dashboard that the role doesn't grant.
tags:
  - reference
  - beta
---

# Dashboards API errors

The Dashboards API uses the error format and the error codes of [Error handling](../error-handling.md#common-error-codes). This page describes what only the Dashboards API returns: its own error codes, the texts in `details`, and a dashboard that the role doesn't grant. This page is for developers who call the [Dashboards API](README.md).

## Error codes of the Dashboards API

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 403 | `insufficient_rights` | The role gives access to Dashboards, but lacks its `dashboards-standard` feature, which both operations need. | Ask your organization's administrator for a role that includes the `dashboards-standard` feature. |
| 503 | `tool_unavailable` | Navixy is reading as many dashboards as it can at once, or the request for dashboard data took too long. A slow request can also end with an HTTP 5xx error without a JSON body. | Repeat the request after some seconds. See [How to retry a request](../error-handling.md#how-to-retry-a-request). |

A request that took too long counts toward the daily limit, and one refused because Navixy is busy doesn't. See [Daily limits](../limits.md#daily-limits).

## Details of a wrong field

When a field of the body is wrong, the response is HTTP 400 with `invalid_tool_call`, and `details` names the field. The Dashboards operations write these texts:

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

### How to handle a wrong dashboard or period

Take the dashboard codes and the period IDs from the response of [List dashboards and periods](api-reference.md), as [Dashboard data](dashboard-data.md#list-the-dashboards-and-periods) describes. Send `range` only for Trips and Conditions. For the general steps, see [How to handle a wrong field](../error-handling.md#how-to-handle-a-wrong-field).

## How to handle a dashboard that the role doesn't grant

A dashboard that the role doesn't grant isn't refused with HTTP 403. [List dashboards and periods](api-reference.md) leaves it out. A request for its data returns HTTP 400 with `invalid_tool_call`, and `details.dashboard` lists the codes that the token can read, or is `this user may open no dashboard`. Ask your organization's administrator for a role that grants the dashboards that your app needs.

## See also

* [Error handling](../error-handling.md#common-error-codes): The error format and the error codes of both APIs.
* [Limits](../limits.md#daily-limits): The daily limits of both APIs and how requests are counted.
* [Dashboard data](dashboard-data.md): The body fields that `details` names.
