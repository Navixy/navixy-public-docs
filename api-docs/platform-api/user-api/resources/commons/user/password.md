---
title: User password
description: Change a password with the current one, or set one from a restore link.
---

# User password

Two operations change a password, and which to use depends on what the caller holds.

`password/set` takes the session hash from a password restore email, which is how a forgotten password is reset. `password/change` takes an ordinary session plus the existing password, which is how a signed-in user changes theirs.

Both work only with a standard user session. Calling either with an API key returns error 4, "User or API key not found or session ended", even though the key itself is valid.

{% include "../../../../../../.gitbook/includes/password-requirements.md" %}

## API actions

API base path: `/user/password`.

***

{% hint style="warning" %}
This operation accepts **only** the session hash from a password restore email. Any other hash returns error 4, "User or API key not found or session ended".
{% endhint %}

{% openapi-operation spec="navixy-platform" path="/user/password/change" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 101 - In demo mode this function is disabled, when the session hash belongs to a demo user.

***

{% openapi-operation spec="navixy-platform" path="/user/password/set" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 101 - In demo mode this function is disabled, when the session hash belongs to a demo user.
* 245 - New password must be different, when `old_password` and `new_password` match.
* 248 - Wrong password, when `old_password` is wrong.
