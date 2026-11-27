---
description: >-
  List the report types of a workspace with the API of Reports, and read one,
  with the actions that your role allows for each.
tags:
  - fundamentals
  - beta
---

# Report types

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Reports in a browser. The workspace must have Reports, and the role must let you see the report type. See [API access](../authentication.md#api-access).
{% endhint %}

A report type is what a report shows, such as Trips and stops. The API names report types under the `/reports` path, with a code as the ID: `trips_and_stops`, `sensor_readings`, or `driver_performance`. This page explains what the list of report types returns. The operations are `GET /api/reports` and `GET /api/reports/{id}`. See [List report types](api-reference.md) and [Read report type](api-reference.md) in the API reference.

## What a report type returns

Each report type has its `id`, its `name` in English, and `actions`: what your role allows for it.

| Action | What it allows |
| --- | --- |
| `view` | Read the report type in `GET /api/reports` and `GET /api/reports/{id}` |
| `execute` | Run the report type and read the tracks of its trips. See [Report runs](report-runs.md) |
| `edit` | Change the report type. No operation uses it |

The following request lists the report types:

```bash
curl --request GET \
  --url 'https://reports.<REGION>.navixy.com/api/reports' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response:

```json
[
  { "id": "driver_performance", "name": "Driver performance", "actions": ["view", "execute", "edit"] },
  { "id": "sensor_readings", "name": "Sensor readings", "actions": ["view", "execute", "edit"] },
  { "id": "trips_and_stops", "name": "Trips and stops", "actions": ["view", "execute", "edit"] }
]
```

## How the list and the permissions differ

`GET /api/reports` lists the report types that your role lets you see, ordered by ID. An empty list is a valid response: the role lets you see no report type.

The 2 permissions don't depend on each other. A role that lets you run a report type but not see it can still run it, and that report type isn't in this list. To check which report types you may run, see [Assets and permissions](assets-and-permissions.md).

A report type that your role doesn't let you see returns an error. See [Report types that your role doesn't grant](errors.md#report-types-that-your-role-doesnt-grant).
