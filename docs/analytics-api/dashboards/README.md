---
description: >-
  What your code can read from Navixy Dashboards, and its 2 approaches: API
  operations with a session token, and tools with an API key.
tags:
  - overview
  - beta
---

# Dashboards overview

Your code can read the data of the dashboards in Navixy Dashboards. The dashboards show the fleet's current status, its trips, and the condition of its vehicles and GPS devices. There are 2 approaches. API operations serve a person signed in to Navixy Dashboards, and tools serve integrations and AI agents with a Navixy Console API key. This page is for developers who choose between them.

Both approaches use the address `https://dashboards.<REGION>.navixy.com/api`, where `<REGION>` is `eu` or `us`, and send a token in the `Authorization` header. To get a token, see [Authentication](../authentication.md).

## Which approach to use

<table><thead><tr><th width="150"></th><th>API</th><th>Tools</th></tr></thead><tbody><tr><td>Who uses it</td><td>A person signed in to Navixy Dashboards in a browser, or a script that this person runs</td><td>An integration or an AI agent, with no person signed in</td></tr><tr><td>Token</td><td>The session token of the signed-in person. See <a href="../authentication.md#api-access">API access</a></td><td>The access token from an API key. See <a href="../authentication.md#tools-access">Tools access</a></td></tr><tr><td>Requests</td><td><code>GET</code> requests under <code>/api/dashboards</code> and <code>/api/me/permissions</code></td><td><code>POST</code> requests under <code>/api/tools/</code>, with a JSON body that includes <code>timeZone</code></td></tr><tr><td>What a response has</td><td>A dashboard's definition, or the rows of one panel, in the units of Navixy Dashboards, such as meters and seconds</td><td>What every panel of a dashboard shows, in one response, with units such as kilometers and hours</td></tr><tr><td>Limits</td><td>No daily limit. See <a href="../limits.md#api-limits">API limits</a></td><td>About 250 dashboard reads for each API key in a workspace in 24 hours. See <a href="../limits.md#tools-limits">Tools limits</a></td></tr><tr><td>Start here</td><td><a href="../getting-started/api-with-session-token.md">Getting started with a session token</a></td><td><a href="../getting-started/tools-with-api-key.md">Getting started with an API key</a></td></tr></tbody></table>

Use the API when your code runs for a person who is signed in, such as a script that copies a panel to a spreadsheet. Use the tools when your code runs on its own, such as a nightly export or an AI agent that answers questions about the fleet.

## What you can do

* List the dashboards that a session may open, and read the periods and panels of each one. See [Dashboard definitions](dashboard-definitions.md).
* Read the rows of one panel for a period, as Navixy Dashboards reads them. See [Panel data](panel-data.md).
* Read what every panel of a dashboard shows for a period, with an API key. See [Dashboard data](tools/dashboard-data.md).
* Find the panel that your app needs and the fields of its rows in both approaches. See [Dashboard panels](dashboard-panels.md).

## Key concepts

<table><thead><tr><th width="160">Term</th><th>Definition</th></tr></thead><tbody><tr><td>Dashboard</td><td>A set of panels about one subject. There are 3 dashboards, with the IDs <code>live_status</code>, <code>trips</code>, and <code>conditions</code>. Each workspace has the same 3. To learn what each one shows, see <a href="https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards">Dashboards</a> in the Location Intelligence documentation.</td></tr><tr><td>Panel</td><td>One number, chart, table, or map on a dashboard, such as <strong>Total distance</strong>. A panel has an ID that is unique in its dashboard.</td></tr><tr><td>Period</td><td>The time that Trips and Conditions cover: the last 1, 6, 12, or 24 hours, or the last 7, 14, or 31 days. Each period ends at the moment of the request. Live status has no periods.</td></tr><tr><td>Definition</td><td>API: the description of a dashboard, with its periods and its panels. It's the same in every workspace.</td></tr><tr><td>Panel data</td><td>API: the rows of one panel for one period.</td></tr><tr><td>Dashboard data</td><td>Tools: what every panel of one dashboard shows for one period, with the units of its values.</td></tr></tbody></table>

## How each approach works

With the API, a script reads a dashboard in 3 steps:

1. List the dashboards, with `GET /api/dashboards`.
2. Read the definition of a dashboard, with `GET /api/dashboards/{id}`, for its panel IDs and period IDs.
3. Read each panel that the script needs, with `GET /api/dashboards/{id}/panels/{panelId}`.

With the tools, an integration reads a dashboard in 2 steps:

1. List the dashboards and periods, with `POST /api/tools/context`.
2. Get the dashboard data for a period, with `POST /api/tools/read_dashboard`.

Navixy Dashboards reads its panels again every 90 seconds. To keep the data current, read the dashboard again later. [Dashboard definitions](dashboard-definitions.md) and [Panel data](panel-data.md) describe the API steps, and [Dashboard data](tools/dashboard-data.md) the tools steps.

### Which data a response includes

The data is the data of one workspace: the workspace of the token. Inside that workspace, the data includes only the assets, GPS devices, and geo objects that the role lets you see. For the API, that's the role of the signed-in person. For the tools, it's the role of the API key. So 2 tokens with different roles can get different values from the same dashboard.

Navixy builds trips every 4 hours. So over a period of a few hours, the Trips panels that count trips can return 0 while your vehicles drive.

## Access

The role of the token decides what your app can read. To read a dashboard, both approaches need all of the following:

* The workspace has Dashboards.
* The role includes Dashboards, its `dashboards-standard` feature, and the dashboard. Without the feature, the operations return HTTP 403.
* For the values, the role lets you see the assets, GPS devices, and geo objects.

For a dashboard that the role doesn't grant, see [Dashboards errors](errors.md#a-dashboard-that-the-role-doesnt-grant). To change a role, ask your organization's administrator. See [Access to Dashboards](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards#access-to-dashboards) in the Location Intelligence documentation.

## Limits

The API has no daily limit, and the tools count each dashboard read. See [API limits](../limits.md#api-limits) and [Tools limits](../limits.md#tools-limits).

## Section contents

* [Dashboard definitions](dashboard-definitions.md): API. List the dashboards, read the definition of a dashboard, and read what the session may do.
* [Panel data](panel-data.md): API. Read the rows of one panel for a period, and learn the units of its values.
* [Dashboard panels](dashboard-panels.md): Every panel of Live status, Trips, and Conditions, with its fields in both approaches.
* [Dashboards API reference](api-reference.md): The API operations and their response models.
* [Dashboards tools](tools/README.md): The tool operations and their response models.
* [Dashboard data](tools/dashboard-data.md): Tools. Read what every panel of a dashboard shows for a period, with units, line charts, and periods.
* [Dashboards errors](errors.md): The errors of the API and of the tools.
