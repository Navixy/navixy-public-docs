---
description: >-
  Create a rule through Navixy GraphQL API, turn it on, read it back, change
  it, find the rules of an asset, and delete it.
tags:
  - guide
  - beta
---

# Creating and managing rules

A rule tells the users of a workspace when its equations become true for an asset. This guide builds a speed limit rule for a depot. The rule sends a notification when one of two trucks drives faster than 25 km/h inside the depot during working hours. You list the parameters for the equations, create the rule, turn it on, change it, and delete it.

The guide is for developers who are comfortable with GraphQL and who build a rule editor or automate rules. See [How rules work](../how-rules-work.md) to learn the behavior that the guide relies on, such as the order of equations and why a new rule is turned off.

## Prerequisites

You need an access token for a workspace that has the Rules product. Your roles need the `read`, `create`, `update`, and `delete` actions on `rules.rule`. The API reads the workspace from the token, so no operation on this page asks for a workspace ID. See [Authentication](../../authentication/README.md) to learn how to get a token and how the API chooses the workspace.

A rule checks a device only through the asset that the device is assigned to. Before you create a rule, assign each device to an asset. See [Creating assets and assigning devices](../../business-data-repository/guides/creating-assets-and-assigning-devices.md).

Every request on this page is a GraphQL query or mutation with variables. Send each request as an HTTP POST request to `https://nvx4.<REGION>.navixy.com/api/graphql`. Replace `<REGION>` with the region of your organization, `eu` or `us`. See [Endpoint and region](../../graphql-basics/#endpoint-and-region). Put the text of the operation in the `query` field of a JSON body, and the variables in the `variables` field. The following request lists the rules of your workspace:

```bash
curl -L \
  --request POST \
  --url 'https://nvx4.<REGION>.navixy.com/api/graphql' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query ListRules { rules { rules(first: 10) { nodes { id title isEnabled } } } }"
  }'
```

Response:

```json
{
  "data": {
    "rules": {
      "rules": {
        "nodes": []
      }
    }
  }
}
```

The steps below show only the operation and its variables. In your GraphQL client, send the operation as the query and the variables as its variables.

### Find the assets, the geo object, and the schedule

A rule refers to assets, geo objects, and schedules by ID. All of them belong to your workspace and are stored in Business Data Repository. Read their IDs in one query:

```graphql
query FindRuleInputs {
  bdr {
    assets(first: 10, filter: { titleContains: "Truck" }) {
      nodes { id title }
    }
    geoObjects(first: 10, filter: { titleContains: "Depot" }) {
      nodes { id title geojsonData }
    }
    schedules(first: 10, filter: { titleContains: "Working hours" }) {
      nodes { id title }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "assets": {
        "nodes": [
          { "id": "01a1305f-50ec-8000-801f-41535354a5e1", "title": "Truck 12" },
          { "id": "01a1305f-50ec-8001-801f-41535354b7c2", "title": "Truck 17" }
        ]
      },
      "geoObjects": {
        "nodes": [
          {
            "id": "01a0f2c4-1a2b-8000-801f-47454f4f0d41",
            "title": "Depot",
            "geojsonData": {
              "type": "Polygon",
              "coordinates": [[[13.401, 52.519], [13.409, 52.519], [13.409, 52.523], [13.401, 52.523], [13.401, 52.519]]]
            }
          }
        ]
      },
      "schedules": {
        "nodes": [
          { "id": "01a0f2c4-3c4d-8000-801f-53434844e512", "title": "Working hours" }
        ]
      }
    }
  }
}
```

Check that the geo object's `geojsonData` has the type `Polygon` or `MultiPolygon`. A geo object of any other type is refused with `UNSUPPORTED_GEOMETRY`. Your roles also need `read` on `bdr.asset`, `bdr.geo-object`, and `bdr.schedule`, because a save reports a record that you can't read as unknown.

## How a write describes a rule

`ruleCreate` accepts a `RulesRuleCreateInput` with four fields:

* `title`: The rule's name, up to 255 characters. Navixy removes the spaces at the start and the end, and replaces several spaces in a row with one space.
* `notificationText` (optional): The text of the notification, up to 1,000 characters, sent exactly as written. Without it, the notification shows the `title`.
* `assetIds`: The covered assets, up to 25,000. An empty list covers every asset of the workspace, including the assets added later.
* `equations`: The chain of 1 to 16 equations, in the order that Navixy checks them.

Each equation is one `RulesEquationInput`. An equation with a field of another `kind`, or without a required field of its own `kind`, is refused. Each `kind` accepts these fields:

| `kind` | Fields of the equation |
| --- | --- |
| `PARAMETER` | `field`, `index` (optional), `operator`, `threshold` |
| `GEOFENCE` | `geoObjectId`, `geofenceRelation` |
| `SCHEDULE` | `scheduleId`, `scheduleRelation` |

Every equation after the first also has `connector`: `AND` or `OR`. The first equation has none. For what each field accepts, see [How rules work](../how-rules-work.md).

### Compare a value calculated by IoT Logic

An equation compares a value that an IoT Logic flow calculated in the same way as a parameter that the device sent. Use the attribute name that the flow writes as `field`, and move a channel number at the end of the name to `index`. For example, a flow writes the average fuel level of tank 2 to the attribute `fuel_avg_2`. The following equation is true when the value is under 15:

```json
{
  "kind": "PARAMETER",
  "field": "fuel_avg",
  "index": 2,
  "operator": "LT",
  "threshold": { "value": { "number": "15" } }
}
```

A calculated value has no unit, so the threshold has none either. See [Values calculated by IoT Logic](../how-rules-work.md#values-calculated-by-iot-logic).

## List the parameters for the editor

A parameter equation names a parameter of the device data, such as `speed`. The `parameterFields` query lists the parameters that the devices of the given assets report. Send the rule's `assetIds`. Without them, the query lists the parameters of every asset that you can read, as a rule with an empty list covers every asset:

```graphql
query ListParameters($assetIds: [ID!]) {
  rules {
    parameterFields(assetIds: $assetIds) {
      field
      index
      valueType
      unit
    }
  }
}
```

Variables:

```json
{
  "assetIds": [
    "01a1305f-50ec-8000-801f-41535354a5e1",
    "01a1305f-50ec-8001-801f-41535354b7c2"
  ]
}
```

Response:

```json
{
  "data": {
    "rules": {
      "parameterFields": [
        { "field": "altitude", "index": null, "valueType": "NUMBER", "unit": "m" },
        { "field": "heading", "index": null, "valueType": "NUMBER", "unit": null },
        { "field": "ignition", "index": null, "valueType": "BOOLEAN", "unit": null },
        { "field": "lls_level", "index": 1, "valueType": "NUMBER", "unit": "L" },
        { "field": "lls_level", "index": 2, "valueType": "NUMBER", "unit": "L" },
        { "field": "speed", "index": null, "valueType": "NUMBER", "unit": "km/h" }
      ]
    }
  }
}
```

You can use each entry in an equation:

* Copy `field` and `index` into the equation unchanged.
* `valueType` tells you which field of the threshold's `value` to set: `NUMBER` means `number`, `BOOLEAN` means `boolean`, `STRING` means `string`, `TIMESTAMP` means `timestamp`, and `DURATION` means `duration`. The type also decides which operators the equation accepts.
* `unit` is the unit that the devices report. You can copy it into `threshold.unit` when it's one of the accepted codes, such as `km/h` or `L`.

The list has the parameters that the devices reported, plus `speed`, `altitude`, and `heading` for every asset with a device. It doesn't have every parameter that a device can report:

* A parameter appears only after a device sends it. An empty list doesn't prove that a device lacks a parameter.
* A parameter whose latest value is cleared on every device isn't listed.
* A parameter with byte, array, or structure values isn't listed, because no operator compares such values. Neither is `avl_io`, the raw protocol element of some devices.
* The list can lag a little behind the latest messages.
* The list has names only, no display titles. Show your own labels for the names.

A parameter that devices report with two types or two units appears once for each, so the same `field` and `index` can be in two entries. Let the user choose the entry, because a threshold of the wrong type never matches. `assetIds` accepts up to 25,000 IDs, as many as a rule. More return `VALIDATION_ERROR`.

When Navixy can't read the device data, the query fails with `SERVICE_UNAVAILABLE` instead of returning a list without some devices' parameters. Wait before you retry, and wait longer after each refusal.

## Example scenario: speed limit in the depot during working hours

To create the rule, turn it on, read it back, and change it, follow these steps:

{% stepper %}
{% step %}
### Create the rule

Send the whole rule in one `ruleCreate` mutation. The rule below has three equations joined with `AND`. The speed is over 25 km/h, the truck is inside Depot, and the time is within Working hours.

```graphql
mutation CreateRule($input: RulesRuleCreateInput!) {
  rules {
    ruleCreate(input: $input) {
      rule {
        id
        version
        title
        isEnabled
        assetCount
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "title": "Speeding in the depot",
    "notificationText": "A truck drove faster than 25 km/h in the depot.",
    "assetIds": [
      "01a1305f-50ec-8000-801f-41535354a5e1",
      "01a1305f-50ec-8001-801f-41535354b7c2"
    ],
    "equations": [
      {
        "kind": "PARAMETER",
        "field": "speed",
        "operator": "GT",
        "threshold": { "value": { "number": "25" }, "unit": "km/h" }
      },
      {
        "connector": "AND",
        "kind": "GEOFENCE",
        "geoObjectId": "01a0f2c4-1a2b-8000-801f-47454f4f0d41",
        "geofenceRelation": "INSIDE"
      },
      {
        "connector": "AND",
        "kind": "SCHEDULE",
        "scheduleId": "01a0f2c4-3c4d-8000-801f-53434844e512",
        "scheduleRelation": "WITHIN"
      }
    ]
  }
}
```

Response:

```json
{
  "data": {
    "rules": {
      "ruleCreate": {
        "rule": {
          "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
          "version": 1,
          "title": "Speeding in the depot",
          "isEnabled": false,
          "assetCount": 2
        }
      }
    }
  }
}
```

The rule is saved and turned off: `isEnabled` is `false`. Save `id` and `version` for the next step. A rule that fails validation is refused with a `VALIDATION_ERROR` that lists every problem. See [Rule errors](../rule-errors.md).
{% endstep %}

{% step %}
### Turn on the rule

Send `ruleUpdate` with `isEnabled: true` and the current `version`. Turning on a rule validates the whole rule again, including its assets, geo object, and schedule.

```graphql
mutation EnableRule($input: RulesRuleUpdateInput!) {
  rules {
    ruleUpdate(input: $input) {
      rule {
        id
        version
        isEnabled
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
    "version": 1,
    "isEnabled": true
  }
}
```

Response:

```json
{
  "data": {
    "rules": {
      "ruleUpdate": {
        "rule": {
          "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
          "version": 2,
          "isEnabled": true
        }
      }
    }
  }
}
```

Every successful write raises `version`. Navixy starts checking messages for the rule about 10 seconds later. When a truck goes faster than 25 km/h inside Depot during working hours, the users who can read the truck receive a notification. See [Receiving notifications](receiving-notifications.md).

A rule editor that saves a new rule as turned on sends these two mutations one after the other. If turning on fails, the rule stays saved and turned off. Show the violations, and let the user fix them and turn on the rule again.
{% endstep %}

{% step %}
### Read the rule back

Read the whole rule with `rule(id:)`. An equation's `condition` is one of three types, so select its fields with inline fragments: the `... on TypeName { }` blocks in the query below. A threshold's `value` and `upper` are one of five value types, which the `RuleValue` fragment selects.

```graphql
query GetRule($id: ID!) {
  rules {
    rule(id: $id) {
      id
      version
      title
      notificationText
      isEnabled
      coversAllAssets
      assetIds
      assetCount
      equations {
        connector
        condition {
          __typename
          ... on RulesParameterCondition {
            field
            index
            operator
            threshold {
              value { ...RuleValue }
              upper { ...RuleValue }
              unit
            }
          }
          ... on RulesGeofenceCondition { geoObjectId geofenceRelation }
          ... on RulesScheduleCondition { scheduleId scheduleRelation }
        }
      }
      updatedAt
    }
  }
}

fragment RuleValue on RulesValue {
  ... on RulesNumberValue { numberValue }
  ... on RulesBoolValue { boolValue }
  ... on RulesStringValue { stringValue }
  ... on RulesTimestampValue { timestampValue }
  ... on RulesDurationValue { durationValue }
}
```

Variables:

```json
{
  "id": "01a14c2e-7d10-8000-801f-52554c45a3f1"
}
```

Response:

```json
{
  "data": {
    "rules": {
      "rule": {
        "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
        "version": 2,
        "title": "Speeding in the depot",
        "notificationText": "A truck drove faster than 25 km/h in the depot.",
        "isEnabled": true,
        "coversAllAssets": false,
        "assetIds": [
          "01a1305f-50ec-8000-801f-41535354a5e1",
          "01a1305f-50ec-8001-801f-41535354b7c2"
        ],
        "assetCount": 2,
        "equations": [
          {
            "connector": null,
            "condition": {
              "__typename": "RulesParameterCondition",
              "field": "speed",
              "index": null,
              "operator": "GT",
              "threshold": {
                "value": { "numberValue": "25" },
                "upper": null,
                "unit": "km/h"
              }
            }
          },
          {
            "connector": "AND",
            "condition": {
              "__typename": "RulesGeofenceCondition",
              "geoObjectId": "01a0f2c4-1a2b-8000-801f-47454f4f0d41",
              "geofenceRelation": "INSIDE"
            }
          },
          {
            "connector": "AND",
            "condition": {
              "__typename": "RulesScheduleCondition",
              "scheduleId": "01a0f2c4-3c4d-8000-801f-53434844e512",
              "scheduleRelation": "WITHIN"
            }
          }
        ],
        "updatedAt": "2026-10-28T09:20:31Z"
      }
    }
  }
}
```

`rule(id:)` returns `null` when no rule of your workspace has the ID. The `node(id:)` query returns the same rule as a `RulesRule`.
{% endstep %}

{% step %}
### Change the rule

Send `ruleUpdate` with the rule's `id`, its current `version`, and only the fields to change. A field that you leave out keeps its value. `assetIds` and `equations` are whole lists: the list that you send replaces the stored list.

The update below adds a third truck and changes the text. It sends the full list of assets, the two trucks from before and the new one:

```graphql
mutation ChangeRule($input: RulesRuleUpdateInput!) {
  rules {
    ruleUpdate(input: $input) {
      rule {
        id
        version
        notificationText
        assetCount
      }
    }
  }
}
```

Variables:

```json
{
  "input": {
    "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
    "version": 2,
    "notificationText": "Speed over 25 km/h in the depot. Check the driver.",
    "assetIds": [
      "01a1305f-50ec-8000-801f-41535354a5e1",
      "01a1305f-50ec-8001-801f-41535354b7c2",
      "01a1305f-50ec-8002-801f-41535354c9d3"
    ]
  }
}
```

Response:

```json
{
  "data": {
    "rules": {
      "ruleUpdate": {
        "rule": {
          "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
          "version": 3,
          "notificationText": "Speed over 25 km/h in the depot. Check the driver.",
          "assetCount": 3
        }
      }
    }
  }
}
```

A change other than `isEnabled` validates the resulting rule, whether it's on or off. Every successful write also restarts the checks of messages for the rule's devices. In your editor, don't send `isEnabled: true` with every save of a rule that is already on: the extra write restarts the checks again. A truck that was over the limit before the change can activate the rule again and send a second notification.

To remove the notification text, so that the notification shows the title, send `notificationText: null`. A `null` for any other field is refused. To cover every asset, send `assetIds: []`.
{% endstep %}
{% endstepper %}

## Turn off a rule

To stop a rule without losing it, send `ruleUpdate` with `isEnabled: false` and nothing else:

```json
{
  "input": {
    "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
    "version": 3,
    "isEnabled": false
  }
}
```

The response has the rule with `isEnabled: false` and `version` 4. Turning off validates nothing, so it works even for a rule whose geo object or schedule was deleted. A rule that is turned off still refers to its assets, geo objects, and schedules, so they can't be deleted. See [Assets, geo objects, and schedules that a rule refers to](#assets-geo-objects-and-schedules-that-a-rule-refers-to).

## List and find rules

The `rules` query lists the rules of your workspace in pages. It's ordered by `title` unless `orderBy` sets `CREATED_AT` or `UPDATED_AT`. The filter's fields are combined with AND:

* `titleContains`: Rules whose title contains the text, in any letter case
* `isEnabled`: Only the rules that are on, or only the rules that are off
* `assetId`: The rules that apply to one asset. These are the rules that list the asset and the rules that cover every asset.

The following query finds the rules that are turned on and apply to Truck 12:

```graphql
query RulesOfAsset($assetId: ID!) {
  rules {
    rules(first: 20, filter: { assetId: $assetId, isEnabled: true }) {
      nodes { id title coversAllAssets }
      pageInfo { hasNextPage endCursor }
      total { count }
    }
  }
}
```

Variables:

```json
{
  "assetId": "01a1305f-50ec-8000-801f-41535354a5e1"
}
```

Response:

```json
{
  "data": {
    "rules": {
      "rules": {
        "nodes": [
          {
            "id": "01a14c2e-9e21-8000-801f-52554c45b402",
            "title": "Ignition at night",
            "coversAllAssets": true
          },
          {
            "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
            "title": "Speeding in the depot",
            "coversAllAssets": false
          }
        ],
        "pageInfo": { "hasNextPage": false, "endCursor": "eyJ0IjoiU3BlZWRpbmcifQ" },
        "total": { "count": 2 }
      }
    }
  }
}
```

An asset that you can't read matches no rule. For the page arguments, see [Pagination](../../pagination.md).

## Handle version conflicts

Every `ruleUpdate` and `ruleDelete` sends the `version` that you read. When someone saved the rule after you read it, the write fails with `CONFLICT`, and nothing changes:

```json
{
  "errors": [{
    "message": "Version conflict: expected 3, found 4",
    "path": ["rules", "ruleUpdate"],
    "extensions": {
      "type": "https://api.navixy.com/errors/conflict",
      "title": "Concurrent Modification Conflict",
      "status": 409,
      "detail": "Version conflict: expected 3, found 4",
      "instance": "/graphql",
      "code": "CONFLICT",
      "field": "input.version",
      "expectedVersion": 3,
      "currentVersion": 4,
      "traceId": "7b2e1c9d0a4f4b6e8c1d2e3f4a5b6c7d",
      "timestamp": "2026-10-28T09:41:05Z"
    }
  }],
  "data": null
}
```

### How to handle version conflicts in a rule editor

Read the rule again with `rule(id:)`, apply the user's change to the new version, and send it with `currentVersion`. Tell the user that someone else changed the rule, because their change may no longer fit. See [Optimistic locking](../../optimistic-locking.md).

## Delete a rule

Send `ruleDelete` with the rule's `id` and current `version`. The delete works even when the rule no longer passes validation:

```graphql
mutation DeleteRule($input: RulesRuleDeleteInput!) {
  rules {
    ruleDelete(input: $input) {
      id
    }
  }
}
```

Variables:

```json
{
  "input": {
    "id": "01a14c2e-7d10-8000-801f-52554c45a3f1",
    "version": 4
  }
}
```

Response:

```json
{
  "data": {
    "rules": {
      "ruleDelete": {
        "id": "01a14c2e-7d10-8000-801f-52554c45a3f1"
      }
    }
  }
}
```

The deleted rule stops sending notifications, and no operation returns it again. An ID with no rule returns `NOT_FOUND`, and an older `version` returns `CONFLICT`. No record refers to a rule, so a rule's delete is never refused with `IN_USE`. See [Deleting records](../../deletion.md).

## Assets, geo objects, and schedules that a rule refers to

While a rule that isn't deleted refers to an asset, a geo object, or a schedule, the API refuses to delete that record with `IN_USE`. A rule that is turned off refers to them too. A rule with an empty `assetIds` list names no asset, so it blocks no asset's delete.

The following error is the response to `assetDelete` for Truck 12:

```json
{
  "errors": [{
    "message": "Cannot delete Asset 01a1305f-50ec-8000-801f-41535354a5e1: it is still referenced by Rule (assetIds)",
    "path": ["bdr", "assetDelete"],
    "extensions": {
      "type": "https://api.navixy.com/errors/in-use",
      "title": "Resource In Use",
      "status": 409,
      "code": "IN_USE",
      "entityType": "ASSET",
      "entityId": "01a1305f-50ec-8000-801f-41535354a5e1",
      "blockers": [
        {
          "entityType": "RULES_RULE",
          "path": "assetIds",
          "ids": ["01a14c2e-7d10-8000-801f-52554c45a3f1"]
        }
      ],
      "hasMoreBlockers": false,
      "traceId": "2f8d6a4c1e0b4d9a8c7e6f5a4b3c2d1e",
      "timestamp": "2026-10-28T10:02:17Z"
    }
  }],
  "data": null
}
```

Each entry in `blockers` has a `path`, which is the rule field to change: `assetIds` for an asset, and `equations` for a geo object or a schedule. `ids` lists the rules only when your roles have `read` on `rules.rule`. Otherwise it's empty.

### How to handle a delete blocked by a rule

Remove the reference from each rule in `ids`, then delete the record again. For an asset, send `ruleUpdate` with an `assetIds` list without it. For a geo object or a schedule, send `ruleUpdate` with an `equations` list without the equation that names it. Or delete the rule. Don't send `assetIds: []` to remove the last asset: an empty list makes the rule cover every asset.

## What you see with limited access

Your access to assets, geo objects, and schedules decides what you see of a rule and what you can change:

* `assetIds` and `assetCount` have only the covered assets that your roles let you read. The other assets stay covered. Don't show `assetCount` as the number of assets that a rule covers.
* The `assetIds` list that you send replaces only the assets that you can see. The assets that you can't see stay in the rule.
* An equation whose geo object or schedule you can't read shows the ID, which `node(id:)` doesn't return for you. When you send that equation back, it's refused as unknown. You can only remove such an equation. In your editor, show it as an equation that the user can't see, not as a broken equation.
* You can rename a rule, turn it on, or turn it off without access to its references. Turning on still validates that every reference exists and covers an area.

No query tells you in advance which actions your roles allow. Handle `PERMISSION_DENIED` on every rules operation.

## Errors in this guide

This guide's operations return these errors:

| Code | When |
| --- | --- |
| `VALIDATION_ERROR` | A malformed field (`extensions.field`), or a rule that fails validation (`extensions.violations`). See [Rule errors](../rule-errors.md). |
| `NOT_FOUND` | `ruleUpdate` or `ruleDelete` names an ID with no rule in your workspace. |
| `CONFLICT` | `ruleUpdate` or `ruleDelete` sends an older `version`. See [Handle version conflicts](#handle-version-conflicts). |
| `PRODUCT_DISABLED` | Your workspace doesn't have the Rules product. `productCode` is `rules`. |
| `PERMISSION_DENIED` | Your roles don't have the action on `rules.rule`. |
| `SERVICE_UNAVAILABLE` | `parameterFields` can't read the device data. |

`rules` can't be `null`, and neither can its fields `rules`, `events`, and `parameterFields`. An error on any of them, or on a rules mutation, sets the whole `data` to `null`, including other fields of the same request. Send `parameterFields` in a request of its own, apart from your Business Data Repository lookups. For the error format, see [Error handling](../../error-handling.md).

## See also

* [How rules work](../how-rules-work.md): Learn which messages a rule checks and when it sends a notification.
* [Reading rule history](reading-rule-history.md): Find out when a rule activated and which values met its equations.
* [Receiving notifications](receiving-notifications.md): Show the notifications that your rules send.
* [Rules](../api-reference/rules/rules.md): Look up every field and input of a rule.
