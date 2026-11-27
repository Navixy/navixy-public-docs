---
description: >-
  The limits of the Analytics API: how many report runs and dashboard requests
  an API key can make in a day, the limits of each request, and token lifetime.
tags:
  - reference
  - beta
---

# Limits

This page lists the limits of the Reports API and the Dashboards API. The limits keep the report data available to every workspace. Plan your app's requests with them in mind.

## Daily limits

Each API key has a daily limit in each workspace. The limit counts the requests of the last 24 hours, so each request counts until it's 24 hours old.

| API | What counts | Daily limit |
| --- | --- | --- |
| Reports API | Each request to `POST /api/tools/run_report_totals`. Listing report types and creating report links don't count | About 250 for each API key in a workspace |
| Dashboards API | Each request to `POST /api/tools/read_dashboard`. Listing dashboards and periods doesn't count | About 250 for each API key in a workspace |

A request counts once the API accepts it, before it reads any data. It counts even if it then fails or takes too long:

* Reports API: a run that returns HTTP 500, or HTTP 503 with `report_source_unavailable`, counts. So does a report that is too large for one run, which returns HTTP 400 with `size` in `details`.
* Dashboards API: a request counts when a panel's data can't be read, and when the response takes too long. A request that took too long can go on running on the server for a while, so wait before you repeat it.

A request doesn't count when it returns HTTP 400 for a wrong field, HTTP 429, or HTTP 503 with `tool_unavailable` because the API is busy. The limit is approximate: when 2 requests arrive at the same moment, both can pass at the limit.

Past the limit, a request returns HTTP 429 with `tool_rate_limited` and reads no data. Wait until older requests are more than 24 hours old, or make fewer requests.

## Request limits

| Limit | Reports API | Dashboards API |
| --- | --- | --- |
| Longest period | 31 days, both days included, and not after today in the time zone of the request | 31 days, as the `31d` period |
| Assets in a request | 500 asset IDs, or `"all"`. A report link has at most 200 | All assets that the role lets you see |
| Lines in a response | 50 asset or sensor lines. `assetsOmitted` or `seriesOmitted` counts the rest | 50 rows for each panel. `rowsLeftOut` counts the rest |
| Assets in the list of report types and assets | Listed only when there are at most 200 | Not applicable |

A report that is too large for one run returns HTTP 400 with `size` in `details`. Run it for a shorter period or for fewer assets. For the details of each API, see [Report summary](reports/report-summary.md) and [Dashboard data](dashboards/dashboard-data.md).

A request that takes too long can end with an HTTP 5xx error. See [How to retry a request](error-handling.md#how-to-retry-a-request).

## Access token lifetime

An access token expires after the number of seconds in `expires_in` of the exchange response, 300 today. The exchange returns no refresh token. Exchange the API key again before the token expires, or when a request returns HTTP 401. The API key itself works until its expiration date or until someone deletes it. See [Authentication](authentication.md).

## See also

* [Error handling](error-handling.md)
* [Authentication](authentication.md)
* [Reports API overview](reports/README.md)
* [Dashboards API overview](dashboards/README.md)
