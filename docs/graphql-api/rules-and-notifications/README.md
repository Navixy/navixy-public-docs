---
description: >-
  Rules and notifications in Navixy GraphQL API: set conditions for your
  assets, read the history of each rule, and receive notifications live.
tags:
  - overview
  - beta
---

# Rules and notifications overview

**Rules and notifications** tell the users of a workspace when something happens to an asset. It's one of the products served by [Navixy GraphQL API](../). A rule is a set of equations for some or all of the workspace's assets. An example is "speed over 90 km/h inside the depot during working hours". Navixy checks every message from the devices of those assets. When the equations become true for an asset, Navixy records the change and sends one notification to the users who can read that asset.

The API has two namespaces for the product. A namespace is a field that groups related operations. The `rules` field of the `Query` and `Mutation` types has the operations that create, change, list, and delete rules. It also has the query that reads each rule's history. The `notifications` field has the query that lists stored notifications and the mutation that marks them as read. The `notificationsEvents` subscription, the notification stream, delivers each notification live.

Every operation reads or changes the data of the workspace named by your access token, so no operation has a workspace ID argument. The shared pages of this space cover the request format, [authentication](../authentication/README.md), [pagination](../pagination.md), and [error handling](../error-handling.md). For your first request, see [Getting started](../getting-started.md).

Users of Location Intelligence create the same rules in the **Notification rules** screen and see the notifications on the map. See [Rules and notifications](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/rules-and-notifications) in the Location Intelligence documentation to learn how users work with rules.

## Purpose and capabilities

Rules and notifications enable you to:

* List the parameters that the devices of your assets report, to offer them in a rule editor
* Create a rule with up to 16 equations, for some assets or for every asset of the workspace
* Turn a rule on and off, change it, and delete it
* Find the rules that apply to one asset
* Read when a rule activated or deactivated for each asset, and which values met its equations
* Receive each notification live, in every open session of a user
* List the notifications that a user missed, count the unread notifications, and mark them as read

## Key concepts

<table><thead><tr><th width="180">Term</th><th>Definition</th></tr></thead><tbody><tr><td><strong>Rule</strong></td><td>A set of equations for some or all of a workspace's assets, with the text of the notification that it sends. A rule is created turned off. Navixy checks messages against it only after you turn it on.</td></tr><tr><td><strong>Equation</strong></td><td>One check in a rule. An equation compares a device parameter with a value, checks the asset's position against a geo object, or checks the message time against a schedule. A rule has 1 to 16 equations.</td></tr><tr><td><strong>Connector</strong></td><td><code>AND</code> or <code>OR</code>. Every equation after the first has a connector, which joins the equation to the result of the equations before it.</td></tr><tr><td><strong>Covered assets</strong></td><td>The assets that a rule checks. An empty list covers every asset of the workspace, including the assets added later. Navixy checks a device only through the asset that the device is assigned to.</td></tr><tr><td><strong>Activation</strong></td><td>The moment when a rule's equations become true for an asset. The rule then stays active for that asset until the equations are true for none of the asset's devices. That moment is a <strong>deactivation</strong>.</td></tr><tr><td><strong>Rule event</strong></td><td>One row of the rule's history: an activation or a deactivation, with the device, the message, and the values that met the equations.</td></tr><tr><td><strong>Notification</strong></td><td>The message that an activation sends to the users who can read the asset. A deactivation sends nothing.</td></tr><tr><td><strong>Read mark</strong></td><td>The time up to which a user marked the notifications of a workspace as read. A notification published later is unread.</td></tr></tbody></table>

## Key workflow

A rule goes through the same steps whether you build it in Location Intelligence or through the API:

1. List the parameters that the devices of the covered assets report, to choose the parameter for each equation.
2. Create the rule with its equations and covered assets. The rule starts turned off.
3. Turn on the rule. Navixy validates the whole rule again and starts checking messages about 10 seconds later.
4. When the equations become true for an asset, the users who can read the asset receive a notification on the notification stream. A user who wasn't connected finds it in the list of stored notifications.
5. Read the rule's history to see when it activated and deactivated, and which values met its equations.
6. Change the rule, turn it off, or delete it when you no longer need it.

See [How rules work](how-rules-work.md) to learn the behavior behind each step. See [Creating and managing rules](guides/creating-and-managing-rules.md) for every step with a request and a response.

## Who does what

Three parties share the work on the rules of a workspace:

<table><thead><tr><th width="200">Who</th><th>Does</th></tr></thead><tbody><tr><td><strong>You</strong></td><td>Create and change rules, turn them on and off, read their history, and show notifications to your users. Every call reads or changes the data of the workspace named by the token.</td></tr><tr><td><strong>Business Data Repository</strong></td><td>Stores the assets, geo objects, and schedules that a rule refers to by ID. While a rule refers to an asset, a geo object, or a schedule, the API refuses to delete that record.</td></tr><tr><td><strong>Navixy Console</strong></td><td>Turns on the Notifications product for your workspace and gives you your roles. An administrator of your organization does both.</td></tr></tbody></table>

Your roles decide what you can do with rules. Rules use the Business Data Repository resource `bdr.rule`:

| Operation | Actions needed on `bdr.rule` |
| --- | --- |
| `rule`, `rules`, `events`, `parameterFields` | `read` |
| `ruleCreate` | `create` |
| `ruleUpdate`, including turning a rule on and off | `update` and `read` |
| `ruleDelete` | `delete` and `read` |

A role with one of these actions has it on every rule of the workspace. Without the action, the operation fails with `PERMISSION_DENIED`. Rules don't need a Navixy Console product of their own. Like every `bdr` operation, they need Business Data Repository. You can create rules and read their history without the Notifications product. The workspace needs Notifications only for the notification stream and the stored notifications.

What you see of a rule's assets, geo objects, and schedules depends on your access to them in Business Data Repository. A rule shows only the covered assets that your roles let you read, and the history lists only the events of those assets. The other assets stay covered.

Notifications are for users. A user receives the notifications about the assets that their roles let them read: on the map in Location Intelligence, and on the notification stream of your app. The workspace also needs the Notifications product. See [Rules and notifications](../authentication/roles-and-products.md#rules-and-notifications) in Roles and products.

An API key can't open the stream or read the stored notifications. An app that works with an API key confirms that a rule activated by reading the rule's history, which needs only `read` on `bdr.rule`. See [Reading rule history](guides/reading-rule-history.md).

## Navigation

The Rules and notifications documentation has three sections: concepts, guides, and the API reference.

### Concepts

These pages explain how rules behave and what a write can return:

* [How rules work](how-rules-work.md): Which messages a rule checks, how equations combine, when a rule activates, and when Navixy sends a notification
* [Rule errors](rule-errors.md): The structure of a refused save, every violation code with its fix, and the other errors of rules and notifications

### Guides

These guides walk through the most common scenarios:

* [Creating and managing rules](guides/creating-and-managing-rules.md)
* [Reading rule history](guides/reading-rule-history.md)
* [Receiving notifications](guides/receiving-notifications.md)

### API reference

The [Rules and notifications API reference](api-reference/README.md) gives the complete technical specification for the GraphQL types and operations of both namespaces:

* [Rules](api-reference/rules/rules.md)
* [Rule history](api-reference/rules/history.md)
* [Notification stream](api-reference/notifications/stream.md)
* [Stored notifications](api-reference/notifications/history.md)
