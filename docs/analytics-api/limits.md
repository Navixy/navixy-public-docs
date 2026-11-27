---
description: >-
  The limits of the Analytics API: the limits of each API request, the daily
  limit and request limits of the tools, and the lifetime of the tokens.
tags:
  - reference
  - beta
---

# Limits

This page lists the limits of the 2 approaches of the Analytics API, the API and the tools. The limits keep the report data available to every workspace. Plan your requests with them in mind.

## API limits

The API has no daily limit. Each request has these limits:

| Limit | Reports API | Dashboards API |
| --- | --- | --- |
| Longest period | A report run covers at most 31 days and an hour, from `from` to `to` | The longest period of the dashboard, `31d`. A period ends at the moment of the request. Live status has no period |
| Assets in a request | 500 asset IDs in `assetIds`. Without `assetIds`, every asset that the role lets you see | Every asset that the role lets you see |
| Size of a response | A run that is too large for one request returns HTTP 422 with `report_too_large`. A Sensor readings series has at most 2,016 points | A table panel returns at most 1,000 rows, and a ranking 10. A panel whose data is too large for one request returns HTTP 503 with `panel_data_unavailable` |

A report run is too large when it needs more than 100,000 rows, or more time or memory than one request may use. A trip track or an asset list over these limits returns HTTP 503 with `report_source_unavailable`. A trip track returns every position of the trip, for trips of the last 3 months.

For a report that is too large, run it for a shorter period or for fewer assets, and join the results in your app. For a panel, choose a shorter period. For the details, see [Report runs](reports/report-runs.md) and [Panel data](dashboards/panel-data.md).

## Tools limits

### Daily limits

Each API key has a daily limit of tool requests in each workspace. The limit counts the requests of the last 24 hours, so each request counts until it's 24 hours old.

| Tools | What counts | Daily limit |
| --- | --- | --- |
| Reports tools | Each request to `POST /api/tools/run_report_totals`. Listing report types and creating report links don't count | About 250 for each API key in a workspace |
| Dashboards tools | Each request to `POST /api/tools/read_dashboard`. Listing dashboards and periods doesn't count | About 250 for each API key in a workspace |

A request counts once the tool accepts it, before it reads any data. It counts even if it then fails or takes too long:

* Reports tools: a request that returns HTTP 500, or HTTP 503 with `report_source_unavailable`, counts. A report that is too large for one request counts too: it returns HTTP 400 with `size` in `details`.
* Dashboards tools: a request counts when a panel's data can't be read, and when the response takes too long. A request that took too long can go on running on the server. Wait before you repeat it.

A request doesn't count when it returns HTTP 400 for a wrong field, HTTP 429, or HTTP 503 with `tool_unavailable` because the tools are busy. The limit is approximate: when 2 requests arrive at the same moment, both can pass at the limit.

Past the limit, a request returns HTTP 429 with `tool_rate_limited` and reads no data. Wait until older requests are more than 24 hours old, or make fewer requests.

### Request limits

| Limit | Reports tools | Dashboards tools |
| --- | --- | --- |
| Longest period | 31 days, both days included, and not after today in the time zone of the request | 31 days, as the `31d` period |
| Assets in a request | 500 asset IDs, or `"all"`. A report link has at most 200 | Every asset that the role lets you see |
| Lines in a response | 50 asset or sensor lines. `assetsOmitted` or `seriesOmitted` counts the rest | 50 rows for each panel. `rowsLeftOut` counts the rest |
| Assets in the list of report types and assets | Listed only when there are at most 200 | Not applicable |

A report that is too large for one request returns HTTP 400 with `size` in `details`. Run it for a shorter period or for fewer assets. For the details, see [Report summary](reports/tools/report-summary.md) and [Dashboard data](dashboards/tools/dashboard-data.md).

A request that takes too long can end with an HTTP 5xx error, with either approach. See [How to retry a request](error-handling.md#how-to-retry-a-request).

## Token lifetime

Both tokens expire after 5 minutes:

* The session token of the API: the open page gets a new one before it expires, so copy the token again when a request returns HTTP 401. See [API access](authentication.md#api-access).
* The access token of the tools: it expires after the number of seconds in `expires_in` of the exchange response, about 300 seconds (5 minutes). The exchange returns no refresh token. Exchange the API key again before the token expires, or when a request returns HTTP 401. The API key itself works until its expiration date or until someone deletes it. See [Tools access](authentication.md#tools-access).

## See also

* [Error handling](error-handling.md)
* [Authentication](authentication.md)
* [Reports overview](reports/README.md)
* [Dashboards overview](dashboards/README.md)
