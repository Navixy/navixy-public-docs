---
description: >-
  Read Navixy report data from your own code: the API for whole reports in
  a signed-in session, and the tools for integrations.
tags:
  - overview
  - beta
---

# Reports overview

Navixy Reports gives your code 2 approaches to the same report data. The API returns whole reports: every trip and stop, sensor series, and driving violations. The tools return report summaries and links that open reports in Location Intelligence, for integrations and AI agents. This page explains what each approach gives and which one to use. To learn what each report shows, see [Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports) in the Location Intelligence documentation.

Both approaches use the address `https://reports.<REGION>.navixy.com/api`, with `eu` or `us` in place of `<REGION>`.

## Which approach to use

| Aspect | API | Tools |
| --- | --- | --- |
| Who uses it | A person in a signed-in Reports session, or a script that this person runs | An integration or an AI agent |
| Token | The session token of a signed-in browser. See [API access](../authentication.md#api-access) | The access token of a Navixy Console API key. See [Tools access](../authentication.md#tools-access) |
| What it returns | Report types, whole report runs with every trip, stop, sensor series, and violation, trip tracks, assets, and permissions | Report types and assets, report summaries, and links to reports |
| Requests | `GET` and `POST` under `/api/reports`, `/api/assets`, and `/api/me` | `POST` under `/api/tools/`, with `timeZone` in every body |
| Limits | See [API limits](../limits.md#api-limits) | See [Tools limits](../limits.md#tools-limits) |

Use the API when a person works in Reports and needs every row of a report. For example, they can export the trips of a week from a signed-in session. Use the tools when your app runs on its own, for example to send the daily mileage of each vehicle to an accounting system. To start with either approach, see [Getting started with the API](../getting-started/api-with-session-token.md) or [Getting started with the tools](../getting-started/tools-with-api-key.md).

## Key concepts

| Term | Definition |
| --- | --- |
| Report type | What a report shows. Each report type has a code as its ID: `trips_and_stops`, `sensor_readings`, or `driver_performance`. |
| Report run | One API request that runs a whole report for a period and a set of assets: `POST /api/reports/{id}/run`. For the tools, each `run_report_totals` request counts toward the daily limit. |
| Report summary | The summary figures of a report for a period: the figures of its **Summary** tab in Location Intelligence, for all assets and for each asset or sensor. Only the tools return it. |
| Trip | A movement of an asset, from the moment it starts to move until it stands still. |
| Stop | A period when an asset stood still. |
| Data period | `dataFrom` and `dataUntil` in an API run. Reports has data for your assets only between these 2 times. |

## What the API gives

The API routes are the routes that the Reports web app uses. Each request needs the session token of a signed-in browser:

* [Report types](report-types.md): List the report types of the workspace with the actions that your role allows for each.
* [Report runs](report-runs.md): Run a whole report for a period of up to 31 days and a set of assets.
* [Trip tracks](trip-tracks.md): Read the positions and speeds of a GPS device during one trip.
* [Assets and permissions](assets-and-permissions.md): List the assets that a run can include, and check what your token allows.
* [Reports API reference](api-reference.md): Look up every API operation, its request and responses, and the response models.

## What the tools give

The tools accept the access token of an API key, and every body includes `timeZone`. See [Time zone](../README.md#time-zone):

* [Reports tools](tools/README.md): Look up the 3 tools and their response models, and the list of report types and assets that the token can use.
* [Report summary](tools/report-summary.md): Learn what a report summary contains for each report type and how to read its figures.
* [Links to reports](tools/links-to-reports.md): Create a link that opens a report in Location Intelligence with the period, the assets, and the view that you chose.

## Access to Reports

The role of your token decides what both approaches return. The workspace must have Reports, and the role must give access to it. Otherwise, a request returns HTTP 403.

A role gives access to each report type separately, with 2 permissions: see the report type, and run it. The API routes check each permission on its own. A role that lets you run a report type but not see it can still run it. The tools accept only the report types that the role lets you run. The role also decides which assets and GPS devices a response includes. To learn how roles are set up, see [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles) in the Navixy Console documentation.

## Section contents

* [Report types](report-types.md): API. List the report types of the workspace and read one.
* [Report runs](report-runs.md): API. Run a whole report for a period and a set of assets.
* [Trip tracks](trip-tracks.md): API. Read the positions and speeds of a GPS device during one trip.
* [Assets and permissions](assets-and-permissions.md): API. List the assets and check what your token allows.
* [Reports API reference](api-reference.md): API. Look up every API operation and its response models.
* [Reports tools](tools/README.md): Tools. Look up the tools, their response models, and the report types and assets that a token can use.
* [Reports errors](errors.md): Look up the errors that only Reports returns, for the API and for the tools.
