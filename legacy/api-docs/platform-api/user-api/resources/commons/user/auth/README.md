---
title: User authentication code
description: The second step of multi-factor authentication, verifying or resending the emailed code.
---

# User authentication code

When an account has multi-factor authentication enabled, [`user/auth`](../README.md#post-user-auth) does not return a usable session. It returns `type: "second_factor_required"` and a limited hash, and emails a code to the address the account registered with.

These two operations finish that exchange: one verifies the code and returns an ordinary session hash, the other sends the code again.

Both require the multi-factor session hash issued part-way through `user/auth`, not an ordinary session hash and not an API key.

## API actions

API base path: `/user/auth/code`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/user/auth/code/verify" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 282 - Wrong authentication code.

***

Resending has its own five-minute rate limit, to prevent abuse.

{% openapi-operation spec="navixy-platform" path="/user/auth/code/resend" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
