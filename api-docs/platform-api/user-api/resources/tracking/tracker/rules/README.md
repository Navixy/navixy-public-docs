---
title: Rules
description: The conditions that make the Navixy platform log an event and notify someone, and the rule types available.
---

# Rules

A rule watches a tracker for a condition and logs an event when the condition is met, which is what a notification is built from. Rules are how movement becomes something an integration can react to.

[Rule](rule.md) covers creating and managing them. [Rule types](rule_types.md) is the catalogue: every type a tracker can carry and the parameters each one takes.

## Operations in this section

<!-- endpoint-reference:start -->

#### Rule

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/tracker/rule/bind`](rule.md#post-tracker-rule-bind) | POST | Bind a rule to trackers |
| [`/tracker/rule/create`](rule.md#post-tracker-rule-create) | POST | Create a rule |
| [`/tracker/rule/delete`](rule.md#post-tracker-rule-delete) | POST | Delete a rule |
| [`/tracker/rule/list`](rule.md#post-tracker-rule-list) | POST | List rules |
| [`/tracker/rule/unbind`](rule.md#post-tracker-rule-unbind) | POST | Unbind a rule from trackers |
| [`/tracker/rule/update`](rule.md#post-tracker-rule-update) | POST | Update a rule |

<!-- endpoint-reference:end -->
