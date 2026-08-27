---
title: Form
description: Structured information collected from a field employee when they complete a task or check in.
---

# Form

A form collects structured information from a field employee at the moment they finish something: a customer's name, a delivery date, a signature, a photo. It is filled from the Mobile Tracker App, and a form attached to a task blocks that task from completing until it is submitted.

A form is single-use. Each one is created from a [template](template.md) and, once filled, is stored with its values for good. That separation is deliberate: a template can change over time without altering any form already filled from it.

This API only attaches and fills forms on **tasks**. Check-in forms are created from the mobile application. For working with task forms, see [Task forms](../task/form/README.md), and for the field and value structures, see [Form fields and values](field-types.md). For a walkthrough, see [how to use task forms](../../../guides/field-service-management/create-forms.md).

## Form object

{% openapi-schemas spec="navixy-platform" schemas="Form" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

The `fields` and `values` entries are described in [Form fields and values](field-types.md).

## Form file object

{% openapi-schemas spec="navixy-platform" schemas="FormFile" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/form`.

***

{% openapi-operation spec="navixy-platform" path="/form/read" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no form with such an ID.

***

{% openapi-operation spec="navixy-platform" path="/form/download" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database - if there is no form with such an ID.

## More in this section

<!-- endpoint-reference:start -->

#### Form templates

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/form/template/list`](template.md#post-form-template-list) | POST | List form templates |
| [`/form/template/create`](template.md#post-form-template-create) | POST | Create form template |
| [`/form/template/read`](template.md#post-form-template-read) | POST | Read form template |
| [`/form/template/update`](template.md#post-form-template-update) | POST | Update form template |
| [`/form/template/delete`](template.md#post-form-template-delete) | POST | Delete form template |
| [`/form/template/stats/read`](template.md#post-form-template-stats-read) | POST | Read template usage |

<!-- endpoint-reference:end -->
