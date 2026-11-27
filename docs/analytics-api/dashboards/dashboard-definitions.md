---
description: >-
  Dashboards API operations: list the dashboards that a session may open, read the
  definition of a dashboard, and read what the session may do.
tags:
  - fundamentals
  - beta
---

# Dashboard definitions

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Dashboards in a browser. See [API access](../authentication.md#api-access).
{% endhint %}

The dashboards of Navixy Dashboards are the same in every workspace: Live status, Trips, and Conditions. This page shows how to list the dashboards, read the definition of a dashboard, and read what the session may do. A definition lists the periods and the panels of a dashboard. [Panel data](panel-data.md) needs the panel IDs and the period IDs from it. This page is for developers who read dashboards in a signed-in session or in a script that the signed-in person runs.

## List the dashboards

The following request lists the dashboards that the session may open:

```bash
curl --request GET \
  --url 'https://dashboards.<REGION>.navixy.com/api/dashboards' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response:

```json
[
  { "id": "live_status", "name": "Live status", "actions": ["view"] },
  { "id": "trips", "name": "Trips", "actions": ["view"] },
  { "id": "conditions", "name": "Conditions", "actions": ["view"] }
]
```

`id` is the dashboard's ID, which the other operations accept in the path. `actions` lists what the session may do with the dashboard: `view` is the only action. The list has only the dashboards that the role grants, in the order of the tabs in Navixy Dashboards. It's empty when the role grants none. The operation is [List dashboards](api-reference.md).

## Read the definition of a dashboard

The definition of a dashboard lists its periods and its panels, with the fields of each panel's rows. The following request reads the definition of Trips:

```bash
curl --request GET \
  --url 'https://dashboards.<REGION>.navixy.com/api/dashboards/trips' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response, shortened to one period and one panel:

```json
{
  "id": "trips",
  "name": "Trips",
  "refreshSeconds": 90,
  "ranges": [
    { "id": "7d", "unit": "day", "count": 7, "stepSeconds": 3600 }
  ],
  "defaultRange": "14d",
  "panels": [
    {
      "id": 11,
      "type": "barchart",
      "title": "Top 10 assets by distance",
      "description": "Assets ranked by total distance traveled over the selected period.",
      "gridPos": { "x": 10, "y": 6, "w": 10, "h": 8 },
      "fields": [
        { "key": "asset", "kind": "text" },
        { "key": "distance", "kind": "distance", "label": "Distance" }
      ]
    }
  ]
}
```

The definition has these parts:

* `refreshSeconds`: how often Navixy Dashboards reads the panels again while the dashboard is open, 90 seconds.
* `ranges` and `defaultRange`: the periods that the panels can cover, and the one that a panel covers when the request has no period. Trips and Conditions have 7 periods, from `1h` to `31d`. Live status has neither field.
* `panels`: each panel with its `id`, its `type`, its texts, its place on the grid, and the `fields` of its rows. The `kind` of a field gives its unit, as [Panel data](panel-data.md#rows-and-values) describes.

A definition is the same in every workspace. Read it once, and keep the panel IDs and the period IDs. [Dashboard panels](dashboard-panels.md) lists every panel and its fields without a request. The operation is [Read dashboard](api-reference.md).

## What the session may do

The following request returns what the session may do in Navixy Dashboards:

```bash
curl --request GET \
  --url 'https://dashboards.<REGION>.navixy.com/api/me/permissions' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response:

```json
{
  "enabled": true,
  "features": ["dashboards-standard"],
  "dashboards": [
    { "id": "live_status", "actions": ["view"] },
    { "id": "trips", "actions": ["view"] },
    { "id": "conditions", "actions": ["view"] }
  ]
}
```

Any valid session token can call this operation, even one whose role grants nothing in Dashboards. Such a session gets HTTP 200, not an error, so the response says what is missing:

* `enabled` is `false`: the workspace doesn't have Dashboards, or the role doesn't include it.
* `features` is empty: the role includes Dashboards but not its `dashboards-standard` feature, which every other operation needs.
* `dashboards` is empty, or lacks a dashboard: the role doesn't grant that dashboard.

The operation is [Read permissions](api-reference.md).

## Access

The workspace must have Dashboards, and the role of the signed-in person must include Dashboards and its `dashboards-standard` feature. Reading a definition also needs a role that grants that dashboard. A dashboard that the role doesn't grant returns HTTP 403, whether it exists or not. To change a role, ask your organization's administrator. For the error codes, see [API errors](errors.md#api-errors).

## See also

* [Panel data](panel-data.md): Read the rows of one panel for a period.
* [Dashboards API reference](api-reference.md): The API operations and their response models.
* [Getting started with a session token](../getting-started/api-with-session-token.md): A first request from a signed-in session.
