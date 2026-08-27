---
title: Form templates
description: The reusable definition a single-use form is created from.
---

# Form templates

A form template is the definition a [form](README.md) is created from. The paper analogy is exact: the template is the printed master, and each form filled from it is one sheet, filled once and then kept as it was.

That separation is why the API works this way. A template's fields can be added, removed, or reordered over time, and none of that changes a form already filled, so an old form always reads exactly as it was submitted. It also means a user attaching a form to a task or a check-in picks a template rather than rebuilding the fields each time.

## Form template object

{% openapi-schemas spec="navixy-platform" schemas="FormTemplate" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The `fields` entries are described in [Form fields and values](field-types.md).

## API actions

API base path: `/form/template`.

Operations that list no errors of their own return only the [general error codes](../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/form/template/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `form_template_update`.

{% openapi-operation spec="navixy-platform" path="/form/template/create" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 101 - In demo mode this function disabled - if current user has "demo" flag.

***

{% openapi-operation spec="navixy-platform" path="/form/template/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no template with such an ID.

***

_Required sub-user rights:_ `form_template_update`.

{% openapi-operation spec="navixy-platform" path="/form/template/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 101 - In demo mode this function disabled - if current user has "demo" flag.
* 201 - Not found in the database - if there is no template with such an ID.

***

_Required sub-user rights:_ `form_template_update`.

{% openapi-operation spec="navixy-platform" path="/form/template/delete" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 101 - In demo mode this function disabled - if current user has "demo" flag.
* 201 - Not found in the database - if there is no template with such an ID.

***

_Required sub-user rights:_ none.

{% openapi-operation spec="navixy-platform" path="/form/template/stats/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if template with the specified ID does not exist.

