---
description: >-
  List the assets that a report run can include, and check what your
  token allows in Navixy Reports.
tags:
  - fundamentals
  - beta
---

# Assets and permissions

{% hint style="info" %}
**API, with a session token**

The operations on this page are API operations. They accept the session token of a person signed in to Navixy Reports in a browser. The asset list needs a role with access to Reports. Any valid session token can read its permissions. See [API access](../authentication.md#api-access).
{% endhint %}

The asset list has the assets that a report run can include. The permissions response says what your token's role allows in Reports: access to the product, and the actions for each report type. Read both before you run a report, so that your app offers only the reports and assets that the token can use. The operations are `GET /api/assets` and `GET /api/me/permissions`. See [List assets](api-reference.md) and [Read permissions](api-reference.md) in the API reference.

## What the asset list returns

The list has the assets of the workspace that your role lets you see, ordered by asset ID. These are the assets whose IDs you can send in the `assetIds` of a report run. See [Report runs](report-runs.md).

| Field | What it stores |
| --- | --- |
| `id` | The ID of the asset, for `assetIds` |
| `title` | The title of the asset, or `null` when Reports has no title for it |
| `devices` | The number of GPS devices of the asset |
| `primaryDevices` | The number of primary GPS devices of the asset |

`devices` and `primaryDevices` are `null` when the role doesn't let you see every GPS device of the workspace. In that case, a run still includes the devices that you can see.

The following request lists the assets:

```bash
curl --request GET \
  --url 'https://reports.<REGION>.navixy.com/api/assets' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response, shortened to 3 assets:

```json
[
  { "id": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f01", "title": "Van 01 (FMB920)", "devices": 1, "primaryDevices": 1 },
  { "id": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f02", "title": "Truck 02 (FMC130)", "devices": 1, "primaryDevices": 1 },
  { "id": "019a7f3e-5c21-7a10-8000-0b2c4d6e8f03", "title": "Bus 03 (FMB125)", "devices": 1, "primaryDevices": 1 }
]
```

The list includes only the assets that Reports has received GPS device data for. An asset without device data isn't listed, although its ID is valid in Navixy GraphQL API. To read and change the assets themselves, use [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/).

## What the permissions response returns

| Field | What it stores |
| --- | --- |
| `enabled` | `true` when the workspace has Reports and the role gives access to it |
| `features` | The features of Reports in the role. `reports-standard` gives access to the report types |
| `templates` | Each report type that the role allows at least one action for, with its `id` and its `actions`: `view` to see it, `execute` to run it, and `edit` to change it |

The following request reads the permissions:

```bash
curl --request GET \
  --url 'https://reports.<REGION>.navixy.com/api/me/permissions' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

Response:

```json
{
  "enabled": true,
  "features": ["reports-standard"],
  "templates": [
    { "id": "trips_and_stops", "actions": ["view", "execute", "edit"] },
    { "id": "sensor_readings", "actions": ["view", "execute", "edit"] },
    { "id": "driver_performance", "actions": ["view", "execute", "edit"] }
  ]
}
```

## How to read the permissions

* **No access.** A token without access to Reports gets HTTP 200 with `enabled` set to `false`, not an error. `enabled: false` has more than one cause: the workspace doesn't have Reports, the role gives no access to it, or Navixy couldn't read the role.
* **The feature.** `templates` is empty when `features` doesn't include `reports-standard`, because every report type request needs that feature.
* **Role, not workspace.** `templates` describes the role, not the workspace. It can name a report type that `GET /api/reports` doesn't return: for example, a report type that the role lets you run but not see. To run a report, check that its `actions` include `execute`.

The errors use the format of every Reports error. See [Reports errors](errors.md#api-errors).
