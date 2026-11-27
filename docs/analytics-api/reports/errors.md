---
description: >-
  The errors that only Navixy Reports returns, for the API routes and for the
  tools: report parameters, report types, trips, and report data.
tags:
  - reference
  - beta
---

# Reports errors

This page lists the errors that only Reports returns, for developers who handle them in their app. It has the errors of the API routes first, then those of the tools. Reports uses the error format and the common error codes of the Analytics API. See [Error handling](../error-handling.md#common-error-codes).

## Report types that your role doesn't grant

The 2 approaches answer differently when your role doesn't let you use a report type:

* **API.** A report type that your role doesn't let you see or run returns HTTP 403. It does so whether or not the workspace has that report type.
* **Tools.** A request for such a report type returns HTTP 400 with `invalid_tool_call`, and `details` has `report`. A role that lets you run no report type gets the same 400. See [How to handle a role without report types](#how-to-handle-a-role-without-report-types).

## API errors

These errors come from the API routes, which accept the session token of a signed-in browser.

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `invalid_report_parameters` | The body of a run has a missing, wrong, or extra field, or the run has no body. `details` names each field and says what it must be. | Fix the fields that `details` names. |
| 401 | None | The request has no session token, or the token is invalid or expired. The token of a Navixy Console API key gets HTTP 401 on these routes too. | Get a new session token. For an API key, use the tools. |
| 403 | `insufficient_rights` | The role gives access to Reports, but not to this report type or this action. | Ask your organization's administrator for a role that lets you see or run this report type. |
| 404 | `template_not_found` | The workspace has no report type with this ID. | Check the ID against `GET /api/me/permissions`. `GET /api/reports` leaves out a report type that your role lets you run but not see. |
| 404 | `trip_not_found` | The workspace has no trip with this ID, or your role doesn't let you see the asset or the GPS device of the trip. A stop ID returns this code too. | Use a trip ID from the `trips` of a run. |
| 422 | `report_too_large` | The report is too large to run. | Run the report for a shorter period or for fewer assets. |
| 501 | `not_implemented` | The report type in a trip track request doesn't serve trip tracks: `trips_and_stops` or `sensor_readings`. | Request the track with `driver_performance`. |
| 503 | `report_source_unavailable` | Report data can't be read at the moment, or a trip track or the asset list is too large to read. | Repeat the request later. See [How to retry a request](../error-handling.md#how-to-retry-a-request). |
| 503 | `report_source_not_configured` | Reports has no report data in this Navixy environment. | Contact Navixy support. |

HTTP 404 with `template_not_found` comes only after the role allows the request.

### How to handle invalid report parameters

A run with wrong fields returns HTTP 400 with `invalid_report_parameters`. `details` names every wrong field at once, and its value says what the field must be. A field that the report type doesn't accept is an error too, so a misspelled `assetIds` never runs the report for every asset:

```json
{
  "code": "invalid_report_parameters",
  "message": "These report parameters are not valid.",
  "details": {
    "to": "must be after from",
    "assetIds": "an array of asset ids, each a UUID",
    "stepSeconds": "one of 300, 600, 1800, 3600, 14400, 86400"
  }
}
```

Only `sensor_readings` accepts `timeZone` and `stepSeconds`. Remove them from the runs of other report types.

### How to handle a run that is too large

A run that is over the limits of Reports returns HTTP 422 with `report_too_large`. Reports never returns part of a report.

Split the run into shorter periods, such as one week at a time, or into smaller sets of assets. Then combine the results in your app. A trip or a stop belongs to the period in which it starts. So when each period starts where the previous one ends, no trip appears twice. See [API limits](../limits.md#api-limits).

## Tools errors

These errors come from the tools, which accept the access token of an API key.

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `invalid_tool_call` with `size` in `details` | The report is too large for one request. The request still counts toward the daily limit. | Request the report for a shorter period or for fewer assets. |
| 403 | `insufficient_rights` | The role gives access to Reports, but lacks its `reports-standard` feature, which gives access to the report types. | Ask your organization's administrator for a role that includes the report types of Reports. |
| 503 | `report_source_unavailable` | Report data can't be read at the moment, or the list of assets is too large to read. | Repeat the request later. See [How to retry a request](../error-handling.md#how-to-retry-a-request). |
| 503 | `report_source_not_configured` | Reports has no report data in this Navixy environment. | Contact Navixy support. |

The following error is a report that is too large for one run:

```json
{
  "code": "invalid_tool_call",
  "message": "The tool call cannot be run as it is.",
  "details": {
    "size": "more data than one run reads: a shorter period or fewer assets"
  }
}
```

### How to handle a report that is too large

Reports never returns part of a report. Request the summary for a shorter period, such as one week at a time, or for fewer assets. Then combine the results in your app. The request that was too large still counts toward the daily limit. See [Tools limits](../limits.md#tools-limits).

### How to handle a role without report types

A role that lets you run no report type isn't refused with HTTP 403. The list of report types and assets has an empty `templates` list. The report summary and the links to reports return HTTP 400 with `invalid_tool_call`, and `details` has `report` set to `this user may run no report`. Ask your organization's administrator for a role that lets you run the report types that your app needs.
