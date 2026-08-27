---
title: Timezone
description: Every timezone the Navixy platform supports, with localized descriptions.
---

# Timezone

This operation returns every timezone the platform supports, with descriptions in the requested locale. It is the list an account picks its timezone from at registration.

It takes no authentication, and accepts none either: a session hash or API key is simply not used. That makes it a useful control when debugging credentials, since it answers whether or not the caller has a working one.

## API actions

API base path: `/timezone`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/timezone/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
