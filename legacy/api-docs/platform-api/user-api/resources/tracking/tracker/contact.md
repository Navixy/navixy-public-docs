---
title: Contact
description: Deprecated. The account's trackers grouped by the contacts that share them.
---

# Contact

{% hint style="warning" %}
**Deprecated.** This action should not be used.
{% endhint %}

Contacts grouped an account's trackers by the people sharing them. The operation still answers, and is documented here because it is still reachable, but nothing new should be built on it.

## API actions

{% hint style="danger" %}
The path is `/contact/list`, at the top level, **not** `/tracker/contact/list`. This page previously documented the tracker-prefixed form, which returns error 111, "Wrong handler".
{% endhint %}

***

{% openapi-operation spec="navixy-platform" path="/contact/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 201 - Not found in the database.
