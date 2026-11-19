---
description: >-
  Why a rule save fails, how the violations list names every problem at once,
  and the other errors that rules and notifications return.
tags:
  - reference
  - beta
---

# Rule errors

When a rule that you save breaks an API requirement, Navixy changes nothing and returns one `VALIDATION_ERROR`. When the input is well formed, the error lists every violation that Navixy found, with the input fields that caused each violation. You can then fix all the violations before you send the save again.

This page is for developers who build a rule editor or another client of the `rules` and `notifications` namespaces. It explains the two kinds of refused save, what to do for each violation code, and the other errors that both namespaces return. For the error format that every operation shares, see [Error handling](../error-handling.md).

## When Navixy validates a rule

Navixy validates a rule at three moments:

* `ruleCreate` validates the new rule.
* `ruleUpdate` validates the rule after the change, whether the rule is on or off. An update that only sets `isEnabled: true` validates the stored rule again, including its references.
* An update that only sets `isEnabled: false` validates nothing, and neither does `ruleDelete`. You can always turn off or delete a rule, even a rule that no longer passes validation.

Turning on a rule can fail even if the rule saved without errors. Two changes after the last save cause it:

* Someone changed the geo object's geometry to a point or a line. Turning on fails with `UNSUPPORTED_GEOMETRY`.
* The geo object or the schedule was deleted at the same moment as the rule was saved. Navixy reports a reference to a deleted record as unknown. At other times, a rule's references can't be deleted. See [Deleting records](../deletion.md).

## Two kinds of refused save

A save is refused in one of two ways, and the error shows which:

* A malformed field fails alone, before Navixy reads the rest of the rule. The error has `extensions.field`, which names the input, and no `violations`.
* A well-formed rule that breaks a requirement for its chain, a threshold, or a reference fails with every violation found. The error has `extensions.violations` and no `field`.

### Malformed fields

Navixy reads the input field by field and stops at the first malformed one. The error names it in `extensions.field`, for example `input.title` or `input.equations[0].field`. These inputs are malformed:

* A `title` that is empty, only spaces, or longer than 255 characters
* A `notificationText` that is empty, only spaces, or longer than 1,000 characters
* A `field` that breaks the naming requirements, or that ends with a channel number and has no `index`
* A negative `index`
* A `unit` that isn't one of the accepted codes, or a `unit` on a threshold that isn't a `number`
* A `number` with more than 38 significant digits, or with the decimal point more than 38 places away from them
* A NUL character in a `string` threshold or in `notificationText`
* An equation with a field of another `kind`, or without a required field of its own `kind`
* A `null` sent for any update field except `notificationText`

In an equation, `index` is the only optional field.

A `RulesValueInput` with none or more than one of its fields set fails before Navixy reads any field. That error has no `extensions.field`. See [Parameter equations](how-rules-work.md#parameter-equations) to learn the naming requirements and the accepted units.

### Structure of a rule validation error

A rule validation error is a standard [validation error](../error-handling.md#validation-error-400) with one extra field, `extensions.violations`. The following error is the response to a create whose second equation has no connector and whose first equation compares a `boolean` with `GT`:

```json
{
  "errors": [
    {
      "message": "Rule is invalid: 2 problems",
      "path": ["rules", "ruleCreate"],
      "extensions": {
        "type": "https://api.navixy.com/errors/validation",
        "title": "Validation Error",
        "status": 400,
        "detail": "Rule is invalid: 2 problems",
        "instance": "/graphql",
        "code": "VALIDATION_ERROR",
        "traceId": "4c1f0e7a9b2d4e6f8a0b1c2d3e4f5a6b",
        "timestamp": "2026-10-28T09:12:44Z",
        "violations": [
          {
            "constraint": "MISSING_CONNECTOR",
            "message": "Every equation after the first needs AND or OR to the one before it",
            "fields": ["input.equations[1].connector"]
          },
          {
            "constraint": "OPERATOR_NOT_ALLOWED",
            "message": "Operator GT is not allowed for a BOOLEAN threshold",
            "fields": ["input.equations[0].operator"]
          }
        ]
      }
    }
  ],
  "data": null
}
```

Each entry of `violations` has three fields:

| Field | Description |
| --- | --- |
| `constraint` | The violation code. The table in [Violation codes](#violation-codes) lists every code. |
| `message` | A description for people. Don't parse it: the text can change. |
| `fields` | The input paths to mark in your editor, such as `input.assetIds` or `input.equations[2].threshold`. |

Navixy always validates the chain of equations and the asset list. It validates the thresholds and the references only when the rule has at most 16 equations and at most 25,000 assets. For a rule over either limit, the error lists only the violations of the chain and the asset list. After you reduce the rule to within the limits, the next save reports the other violations.

### How to handle rule validation errors

To fix a refused save, follow these steps:

1. Read `extensions.code`. When it's `VALIDATION_ERROR`, check for `extensions.field` first and `extensions.violations` second.
2. If the error has `field`, mark that input and show `detail`. Other inputs can still be wrong: Navixy stopped at the first malformed field.
3. If the error has `violations`, mark every path in each entry's `fields`. Show your own text for each `constraint` rather than `message`.
4. Send the corrected rule again. Nothing was saved, so `version` hasn't changed.

## Violation codes

The violation codes cover the chain, the asset list, the thresholds, and the references:

| `constraint` | Input in `fields` | Cause | Fix |
| --- | --- | --- | --- |
| `NO_EQUATIONS` | `input.equations` | The chain is empty. | Add at least one equation. |
| `EQUATIONS_PER_RULE_EXCEEDED` | `input.equations` | The rule has more than 16 equations. | Remove equations, or split the rule into two rules. |
| `CONNECTOR_ON_FIRST_EQUATION` | `input.equations[0].connector` | The first equation has a connector. | Remove `connector` from the first equation. |
| `MISSING_CONNECTOR` | `input.equations[i].connector` | A later equation has no connector. | Set `connector` to `AND` or `OR`. |
| `ASSETS_PER_RULE_EXCEEDED` | `input.assetIds` | `assetIds` has more than 25,000 IDs. | Send fewer assets, or an empty list to cover every asset. |
| `OPERATOR_NOT_ALLOWED` | `input.equations[i].operator` | The operator doesn't fit the threshold type. | Use `EQ` or `NE` for a `boolean` or a `string`. See [Threshold types and operators](how-rules-work.md#threshold-types-and-operators). |
| `RANGE_BOUND_MISSING` | `input.equations[i].threshold` | `IN_RANGE` or `OUT_OF_RANGE` has no `upper`. | Add `upper`. |
| `UNEXPECTED_RANGE_BOUND` | `input.equations[i].threshold` | An operator that isn't a range has `upper`. | Remove `upper`, or use a range operator. |
| `RANGE_BOUNDS_OF_TWO_TYPES` | `input.equations[i].threshold` | `value` and `upper` have different types. | Use the same type for both. |
| `RANGE_OUT_OF_ORDER` | `input.equations[i].threshold` | `upper` is less than `value`. | Swap the two values. |
| `UNKNOWN_ASSET` | `input.assetIds` | An asset doesn't exist in the workspace, or your roles don't let you read it. | Remove the asset, or ask for read access to it. |
| `UNKNOWN_GEO_OBJECT` | `input.equations[i].geoObjectId` | A geo object doesn't exist in the workspace, or your roles don't let you read it. | Choose another geo object. |
| `UNKNOWN_SCHEDULE` | `input.equations[i].scheduleId` | A schedule doesn't exist in the workspace, or your roles don't let you read it. | Choose another schedule. |
| `UNSUPPORTED_GEOMETRY` | `input.equations[i].geoObjectId` | The geo object is neither a `Polygon` nor a `MultiPolygon`. | Choose a geo object that covers an area. |

Navixy returns the same unknown code for a record of another workspace, a deleted record, and a record that your roles don't let you read. The error tells you nothing about records that you can't see.

## Other rules errors

The `rules` operations return these errors besides the validation errors:

| `extensions.code` | When | What to do |
| --- | --- | --- |
| `NOT_FOUND` | `ruleUpdate` or `ruleDelete` names an ID with no rule in the workspace. `rule(id:)` returns `null` instead. | Refresh your list of rules. |
| `CONFLICT` | `ruleUpdate` or `ruleDelete` sends an older `version`. `extensions` has `expectedVersion` and `currentVersion`. | Read the rule again, apply the change to it, and send it with the current version. See [Optimistic locking](../optimistic-locking.md). |
| `PERMISSION_DENIED` | Your roles don't have the action on `bdr.rule`, or your workspace doesn't have Business Data Repository. | Ask your organization's administrator for the action. `node(id:)` returns `null` instead. |
| `SERVICE_UNAVAILABLE` | `parameterFields` can't read the device data. | Wait before you retry, and wait longer after each refusal. |
| `IN_USE` | A delete of an asset, a geo object, or a schedule is refused because a rule refers to it. The `blockers` list has an entry with `entityType` `RULES_RULE`. | Remove the reference from the rule, or delete the rule. See [Deleting records](../deletion.md). |

## Notification errors

The `notifications` query, the `eventsMarkRead` mutation, and the `notificationsEvents` subscription return these errors:

| `extensions.code` | When | What to do |
| --- | --- | --- |
| `PRODUCT_DISABLED` | Your workspace doesn't have the Notifications product. `productCode` is `notifications`. | Ask your organization's administrator to turn on the product in Navixy Console, then get a new token. |
| `PERMISSION_DENIED` | The token belongs to an API key, not a user, or your roles don't let you read any asset. Navixy checks the product first, so in a workspace without Notifications this case returns `PRODUCT_DISABLED`. | Don't retry. The same request returns the same error until your roles change. |
| `UNAUTHORIZED` | The token is invalid or expired, including while a stream is open. | Get a new token and subscribe again. |
| `RATE_LIMITED` | You're over your request rate, or you already have as many open notification streams as the limit allows. | Wait before you retry, and wait longer after each refusal. You can open a new stream after one of your open streams ends. |
| `VALIDATION_ERROR` | The subscription request isn't one subscription, or the connection already has as many subscriptions as it allows. | Fix the request or open another connection. |
| `SERVICE_UNAVAILABLE` | Navixy can't read your workspace or your access while a stream is open. | Subscribe again after a delay. |

`notifications` can't be `null`, so a `PRODUCT_DISABLED` or `PERMISSION_DENIED` error sets the whole `data` to `null`. Every other field in the same request is lost. Send the notifications query in a request of its own. For how each error arrives on the stream, see [Refusals and endings of the stream](guides/receiving-notifications.md#refusals-and-endings-of-the-stream).

## See also

* [Creating and managing rules](guides/creating-and-managing-rules.md): Create a rule and handle the errors of each step.
* [Error handling](../error-handling.md): Learn the error format and every error code of the API.
* [Rules](api-reference/rules/rules.md): Look up the input types that the violation paths name.
