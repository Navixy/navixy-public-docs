---
description: >-
  Read the history of a rule through Navixy GraphQL API: when it activated and
  deactivated for each asset, and why it did or didn't send a notification.
tags:
  - guide
  - beta
---

# Reading rule history

Every time a rule activates or deactivates for an asset, Navixy records one rule event. The event names the rule, the asset, the device whose message caused the change, and the parameter values that met the equations. This guide shows how to list the events of a rule, what each event contains, and how to find out why a rule didn't send an expected notification.

The guide is for developers who build support tools or a history screen, and for scripts that check whether a rule activated. Activation and deactivation are explained in [When a rule activates and deactivates](../how-rules-work.md#when-a-rule-activates-and-deactivates).

## Prerequisites

You need an access token for a workspace that has the Rules product, with the `read` action on `rules.rule`, and the ID of the rule. A token issued for an API key works too. An API key can't read notifications, so the history is how a script confirms that a rule activated. Send every request as described in [Creating and managing rules](creating-and-managing-rules.md#prerequisites): an HTTP POST request to `https://nvx4.<REGION>.navixy.com/api/graphql` with the access token. Replace `<REGION>` with the region of your organization, `eu` or `us`. See [Endpoint and region](../../graphql-basics/#endpoint-and-region).

You see the events of the assets that your roles let you read. The events of other assets aren't listed or counted.

## List the events of a rule

The `events` query of the `rules` field lists rule events in pages. Filter it by rule with `ruleIds`:

```graphql
query RuleHistory($ruleId: ID!) {
  rules {
    events(filter: { ruleIds: [$ruleId] }, first: 20) {
      nodes {
        id
        kind
        assetId
        deviceId
        eventTime
        mayNotify
        activationCounter
        values {
          field
          index
          unit
          deviceId
          eventTime
          value {
            ... on RulesNumberValue { numberValue }
            ... on RulesBoolValue { boolValue }
          }
        }
      }
      pageInfo { hasNextPage endCursor }
      total { count }
    }
  }
}
```

Variables:

```json
{
  "ruleId": "01a14c2e-7d10-8000-801f-52554c45a3f1"
}
```

Response:

```json
{
  "data": {
    "rules": {
      "events": {
        "nodes": [
          {
            "id": "01a14d07-22c8-8000-801f-52455654e1a7",
            "kind": "DEACTIVATION",
            "assetId": "01a1305f-50ec-8000-801f-41535354a5e1",
            "deviceId": "019dbec6-d380-8000-801f-44455643e216",
            "eventTime": "2026-10-28T10:14:52Z",
            "mayNotify": true,
            "activationCounter": null,
            "values": []
          },
          {
            "id": "01a14d05-9b31-8000-801f-52455654c3f0",
            "kind": "ACTIVATION",
            "assetId": "01a1305f-50ec-8000-801f-41535354a5e1",
            "deviceId": "019dbec6-d380-8000-801f-44455643e216",
            "eventTime": "2026-10-28T10:12:07Z",
            "mayNotify": true,
            "activationCounter": 3,
            "values": [
              {
                "field": "speed",
                "index": null,
                "unit": "km/h",
                "deviceId": "019dbec6-d380-8000-801f-44455643e216",
                "eventTime": "2026-10-28T10:12:07Z",
                "value": { "numberValue": "31.4" }
              }
            ]
          }
        ],
        "pageInfo": { "hasNextPage": true, "endCursor": "eyJ0IjoiMjAyNi0xMC0yOFQxMDoxMjowN1oifQ" },
        "total": { "count": 6 }
      }
    }
  }
}
```

Truck 12 drove at 31.4 km/h inside the depot during working hours, so the rule activated for it at 10:12 and sent a notification. At 10:14, the truck no longer met the equations, and the rule deactivated.

The events come newest first by `eventTime`. Events with the same time are ordered by event ID. To get the oldest first, add `orderBy: { field: EVENT_TIME, direction: ASC }`. `total { count }` counts the events that the filter matches. For the page arguments, see [Pagination](../../pagination.md).

### Filter the events

The filter's fields are combined with AND:

* `ruleIds`, `assetIds`, `deviceIds`: Up to 100 IDs each. A list with more than 100 IDs returns `VALIDATION_ERROR`.
* `kinds`: `ACTIVATION`, `DEACTIVATION`, or both.
* `occurredFrom`, `occurredTo`: The period of `eventTime`. Both are inclusive.

An empty list matches nothing, `kinds` included. To read every rule's events for one asset, filter by `assetIds` alone.

The `node(id:)` query returns one event as a `RulesRuleEvent`. It returns `null` for an event of an asset that you can't read.

## What one event records

Each event has these fields:

| Field | What it contains |
| --- | --- |
| `id` | The event's ID |
| `kind` | `ACTIVATION` when the rule activated for the asset, `DEACTIVATION` when it deactivated |
| `ruleId`, `assetId` | The rule and the asset |
| `deviceId` | The device whose message caused the change |
| `messageId` | The ID of that message |
| `eventTime` | When the device recorded the message. A message that the device stored and sent later can be hours or days old, so this time can be long before Navixy recorded the rule event. |
| `mayNotify` | Whether the message could send a notification. See [Late messages in the history](#late-messages-in-the-history). |
| `values` | One entry for each parameter equation that was true: the parameter, its channel, its value and unit, and the device and time of that value |
| `sourceEventId` | Another ID of the activation, which Navixy uses to link the activation to its notification. `null` on a deactivation. |
| `activationCounter` | How many times the rule has activated for this asset, this activation included. `null` on a deactivation. |

A value's `deviceId` can differ from the event's `deviceId`. When a message doesn't have a parameter, Navixy takes it from another device of the same asset, and the entry names that device. See [A parameter that the message doesn't have](../how-rules-work.md#a-parameter-that-the-message-doesnt-have).

Geofence and schedule equations add no entry to `values`. The event records that the whole chain was true, and the rule's equations show which geo object and schedule it checked.

The event doesn't contain the rule's equations as they were. Before you explain an old event with the current equations, compare the event's `eventTime` with the rule's `updatedAt`. A later `updatedAt` means that the equations may have changed after the event.

## Late messages in the history

`mayNotify` shows whether the message that caused the event could send a notification:

* A message that the device sent when the event happened can notify.
* A message that the device stored while it had no connection and sent later can notify only when it arrived within 15 minutes of its event time.

An `ACTIVATION` with `mayNotify: true` means that Navixy passed a notification on for delivery to the users who can read the asset. If the delivery can't start, Navixy retries for up to a day and then gives up. The history doesn't show whether a user received the notification. An `ACTIVATION` with `mayNotify: false` still activated the rule, but sent no notification. When the next message meets the same equations, the rule is already active, so Navixy records nothing. Nobody is notified until the rule deactivates and activates again.

A `DEACTIVATION` has `mayNotify` too, but a deactivation never notifies.

## Why a rule didn't send a notification

The history shows what happened, not what didn't. When an expected notification is missing, check these conditions in order. The first condition that is false explains the missing notification:

1. The rule was on. `isEnabled` is `true`, and was `true` when the message arrived. Navixy checks messages against a rule from about 10 seconds after you turn it on.
2. The asset is covered. `assetIds` lists it, or `coversAllAssets` is `true`. The `rules` query with `filter: { assetId: ... }` lists the rules that apply to an asset.
3. The device is assigned to the asset. A device without an asset is never checked. A device moved to another asset counts there within about a minute.
4. The rule wasn't already active. If the last event for this rule and asset is an `ACTIVATION`, the rule stayed active, and a repeat sends nothing.
5. The equations were true. A parameter equation is false when neither the message nor the asset's recent values have the parameter. It's also false when the name differs from the device data (`lls_level` with `index: 2`, not `lls_level_2`), or when the value's unit differs from `threshold.unit`.
6. The message wasn't late. If the list has an `ACTIVATION` with `mayNotify: false`, the message arrived too late to notify.
7. The message wasn't out of order. Navixy doesn't check a message older than the last message that it processed from the same device. Such a message leaves no event.

If every condition is true and the history has the activation, the problem is in who receives the notification. See [Who receives a notification](receiving-notifications.md#who-receives-a-notification).

## Match a notification with its rule event

A notification has no rule ID, and its `id` differs from the rule event's `id`. To find the rule event behind a notification, list the events with `assetIds` set to the notification's `assetId`. Then pick the `ACTIVATION` whose `eventTime` equals the notification's `occurredAt`. The notification's `title` is the rule's `title`, which narrows the search to one rule.

## Changes to a rule in the history

A change to a rule restarts its checks for the rule's devices. For an asset with two devices, the history can show a `DEACTIVATION` at the first message after the change and an `ACTIVATION` when the other device reports. That activation has a higher `activationCounter` and sent a second notification.

## How long the history is kept

Navixy keeps rule events for 90 days by `eventTime` and deletes older events once a day. An activation whose notification is still waiting for delivery is kept until the delivery starts or Navixy gives up. A late message that is already older than 90 days when it arrives is recorded and then deleted at the next daily pass.

## See also

* [How rules work](../how-rules-work.md): Learn when a rule activates and when it sends a notification.
* [Receiving notifications](receiving-notifications.md): Check what users receive for an activation.
* [Rule history](../api-reference/rules/history.md): Look up every field of a rule event and of the filter.
