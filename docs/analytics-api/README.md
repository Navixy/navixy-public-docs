---
description: >-
  Read report and dashboard data from your own code: the REST APIs of Navixy
  Reports and Dashboards, their addresses, and how to call them with an API key.
tags:
  - overview
  - beta
---

# Analytics API

The Analytics API lets your app read the same data that Navixy Reports and Dashboards show in Location Intelligence. Your app can run a report for a period and a set of assets. It gets the summary of the report for the fleet and for each asset. It can also read a whole dashboard for a period. For example, an app can export the daily mileage of each vehicle to your accounting system or show a fleet panel on your own website.

The Analytics API has 2 REST APIs, one for each product. Each API has its own address and its own reference. Both APIs use the same access token as [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/), which your app gets by exchanging an API key. See [Authentication](authentication.md). To make your first requests, see [Getting started](getting-started.md).

## The 2 APIs

<table><thead><tr><th width="160">API</th><th width="300">Address</th><th>What it returns</th></tr></thead><tbody><tr><td><a href="reports/README.md">Reports API</a></td><td><code>https://reports.&#x3C;REGION>.navixy.com/api</code></td><td>Report types and assets, report summaries for a period, and links that open a report in Location Intelligence</td></tr><tr><td><a href="dashboards/README.md">Dashboards API</a></td><td><code>https://dashboards.&#x3C;REGION>.navixy.com/api</code></td><td>The dashboards of a workspace and their periods, and the dashboard data of a whole dashboard for a period</td></tr></tbody></table>

Replace `<REGION>` with the region of your organization: `eu` for the European Union or `us` for the United States. Use the region of the identity service that issued your token.

## How it works

Every request is an HTTP `POST` request to one of the 2 addresses, with an access token in the `Authorization` header and a JSON body. Every body includes `timeZone`. See [Time zone](#time-zone).

The token gives access to one workspace, so the response has the data of that workspace only. The API key's role decides which reports and dashboards your app can read. See [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles) in the Navixy Console documentation.

The workspace must have the product: the Reports API needs Reports, and the Dashboards API needs Dashboards. To learn what each product shows, see [Reports](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports) and [Dashboards](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards) in the Location Intelligence documentation.

### Time zone

Every request body includes `timeZone`, the time zone of your app's user, as an IANA name such as `Europe/Berlin`. The API uses it in 2 ways:

* The Reports API reads the days of a period on the calendar of this time zone. For example, `last_week` starts on Monday in Berlin. A Dashboards API period, such as `7d`, ends at the moment of the request.
* It writes every date and time of the response in this time zone, with its offset, such as `2026-11-27T16:07:52+01:00`. A time in `UTC` ends in `Z`.

`UTC` is accepted. A fixed offset, such as `+01:00` or `Etc/GMT+2`, returns HTTP 400 with `invalid_tool_call`. A wrong `timeZone` is reported alone, before the other fields of the body are checked.

## Analytics API or GraphQL API

Use the Analytics API to read what Reports and Dashboards calculate: report summaries and dashboard data. Use [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/) to read and change the records that these products calculate from, such as assets, devices, and geo objects. One access token works for both APIs.

## Section contents

* [Getting started](getting-started.md): Make your first requests, from an API key to a report summary and dashboard data.
* [Authentication](authentication.md): How to get an access token from an API key and send it with every request.
* [Error handling](error-handling.md): The error format, the codes that both APIs return, and how to retry a request.
* [Limits](limits.md): How many requests an API key can make in a day, and the limits of each request.
* [Reports API](reports/README.md): Get the report types and assets that your token can use, report summaries, and links that open reports in Location Intelligence.
* [Dashboards API](dashboards/README.md): List the dashboards and periods that the token can read, and read the data of each dashboard.

## Developer resources

* [Reports API reference](reports/api-reference.md) and [Dashboards API reference](dashboards/api-reference.md): every operation and response model.
* The OpenAPI specifications: [Reports API](https://openapi.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/spec/analytics-reports.json) and [Dashboards API](https://openapi.gitbook.com/o/YVLWhgAwCZPoU5vlRsCs/spec/analytics-dashboards.json), to load into an API client or a code generator.
* [Contact Navixy](https://www.navixy.com/en/contact?utm_source=navixy-docs&utm_campaign=analytics-api) for help with the Analytics API.
