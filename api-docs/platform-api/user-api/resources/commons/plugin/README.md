---
title: Plugin
description: Software modules that change how API calls behave, and which of them an account may use.
---

# Plugin

A plugin is a software module that changes how an API call behaves. Registering a tracker and generating a report both run through one: the plugin decides what extra parameters the call takes and what it does with them.

Which plugins an account may use varies, depending on platform settings and purchased features, and only those can be named in a call. This operation is how an integration finds out which it has.

## Plugin object

{% openapi-schemas spec="navixy-platform" schemas="Plugin" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The report plugins have their own page, [report plugins](report_plugins.md), which documents the parameters each one takes.

## API actions

API base path: `/plugin`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/plugin/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Standalone-specific

When neither the user nor their dealer has any plugin enabled, the defaults apply instead, set by the `plugin.tracker.register.defaultIds` and `plugin.tracker.report.defaultIds` configuration options.
