---
description: >-
  The errors that only the Navixy Reports API returns: report data that can't
  be read, a report that is too large, and roles without report types.
tags:
  - reference
  - beta
---

# Reports API errors

This page lists the errors that only the Reports API returns, for developers who handle them in their app. The Reports API uses the error format and the common error codes of the Analytics API. See [Error handling](../error-handling.md#common-error-codes).

## Error codes of the Reports API

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `invalid_tool_call` with `size` in `details` | The report is too large for one report run. The run still counts. | Request the report for a shorter period or for fewer assets. |
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

Reports never returns part of a report. Request the summary for a shorter period, such as one week at a time, or for fewer assets. Then combine the results in your app. The run that was too large still counts. See [Daily limits](../limits.md#daily-limits).

## How to handle a role without report types

A role that lets you run no report type isn't refused with HTTP 403. The list of report types and assets has an empty `templates` list. The report summary and the links to reports return HTTP 400 with `invalid_tool_call`, and `details` has `report` set to `this user may run no report`. Ask your organization's administrator for a role that lets you run the report types that your app needs.
