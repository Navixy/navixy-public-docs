---
description: >-
  Read report and dashboard data from your own code: the 2 approaches of the
  Analytics API, the API and the tools, and which one to use.
tags:
  - overview
  - beta
---

# Analytics API

The Analytics API lets you read the same data that Navixy Reports and Dashboards show in Location Intelligence. It returns report runs with their trips, stops, and sensor readings, report summaries, and the data of dashboard panels. Each product, Reports and Dashboards, has its own address and its own reference.

## The 2 approaches

The Analytics API has 2 approaches. They differ in who uses them, how they authenticate, and what they return:

| | API | Tools |
| --- | --- | --- |
| Who it's for | A person who works in a signed-in Reports or Dashboards session, and scripts that this person runs | Integrations and AI agents that run on their own |
| Authentication | The session token of the signed-in browser | An access token from a Navixy Console API key |
| What it returns | The full data: report runs with every trip, stop, sensor reading, and violation, trip tracks, and the rows of each dashboard panel | Compact results: report summaries, links that open a report in Location Intelligence, and the data of a whole dashboard with converted units |
| Paths | `/api/reports`, `/api/assets`, `/api/dashboards`, `/api/me/permissions` | `/api/tools/` |

A tool is an operation for integrations and AI agents. One tool request returns a result that is ready to read, such as the summary of a report. To start with either approach, see [Getting started](getting-started/README.md). To learn how each one authenticates, see [Authentication](authentication.md).

## Addresses

<table><thead><tr><th width="160">Product</th><th width="300">Address</th><th>Approaches</th></tr></thead><tbody><tr><td><a href="reports/README.md">Reports</a></td><td><code>https://reports.&#x3C;REGION>.navixy.com/api</code></td><td>API and tools</td></tr><tr><td><a href="dashboards/README.md">Dashboards</a></td><td><code>https://dashboards.&#x3C;REGION>.navixy.com/api</code></td><td>API and tools</td></tr></tbody></table>

Replace `<REGION>` with the region of your organization: `eu` for the European Union or `us` for the United States.

## How it works

Every request sends a token in the `Authorization` header. The token gives access to one workspace. The response has the data of that workspace only. The role decides which reports and dashboards you can read: the role of the person who is signed in, or the role of the API key. See [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles) in the Navixy Console documentation.

The workspace must have the product: Reports for the Reports API and tools, and Dashboards for the Dashboards API and tools. To learn what each product shows, see [Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports) and [Dashboards](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards) in the Location Intelligence documentation.

### Time zone

Every request to a tool includes `timeZone`, the time zone of your app's user, as an IANA name such as `Europe/Berlin`. The tools use it in 2 ways:

* The Reports tools read the days of a period on the calendar of this time zone. For example, `last_week` starts on Monday in Berlin. A period of the Dashboards tools, such as `7d`, ends at the moment of the request.
* They write every date and time of the response in this time zone, with its offset, such as `2026-11-27T16:07:52+01:00`. A time in `UTC` ends in `Z`.

`UTC` is accepted. A fixed offset, such as `+01:00` or `Etc/GMT+2`, returns HTTP 400 with `invalid_tool_call`. A wrong `timeZone` is reported alone, before the other fields of the body are checked.

The API writes times in UTC. A report run names its period with dates and times that include an offset, and a dashboard panel names its period with `range`. Only a Sensor readings run accepts `timeZone`, for the steps of its series.

## Analytics API or GraphQL API

Use the Analytics API to read what Reports and Dashboards calculate: report runs, report summaries, and dashboard data. Use [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/) to read and change the records that these products calculate from, such as assets, devices, and geo objects. The access token of an API key works for the tools and for Navixy GraphQL API.

## Section contents

* [Authentication](authentication.md): How to get the token of each approach and send it with every request.
* [Getting started](getting-started/README.md): Make your first requests with the API or with the tools.
* [Error handling](error-handling.md): The error format, the codes that both approaches return, and how to retry a request.
* [Limits](limits.md): The limits of the API and of the tools.
* [Reports](reports/README.md): Run reports and read trip tracks with the API, and get report summaries and links to reports with the tools.
* [Dashboards](dashboards/README.md): Read dashboards and the rows of their panels with the API, and the data of a whole dashboard with the tools.

## Developer resources

* [Reports API reference](reports/api-reference.md) and [Dashboards API reference](dashboards/api-reference.md): every API operation and response model.
* [Reports tools](reports/tools/README.md) and [Dashboards tools](dashboards/tools/README.md): every tool and response model.
* The OpenAPI specifications: [Reports](https://openapi.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/spec/analytics-reports.json) and [Dashboards](https://openapi.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/spec/analytics-dashboards.json), to load into an API client or a code generator.
* [Contact Navixy](https://www.navixy.com/en/contact?utm_source=navixy-docs&utm_campaign=analytics-api) for help with the Analytics API.
