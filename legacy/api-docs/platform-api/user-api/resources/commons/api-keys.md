---
title: API keys
description: Long-lived credentials for integrations, used in place of a user session hash.
---

# API keys

An API key is the credential an integration authenticates with. It works exactly like a session hash obtained from [`user/auth`](user/README.md#post-user-auth), except that it never expires.

That difference matters in several ways:

* A key survives the user logging out or changing their password.
* A key never needs [renewing](user/session/README.md#post-user-session-renew).
* A key means the integration never handles the account's username and password.
* A key can be revoked on its own the moment it is suspected of being compromised.
* Each integration can hold its own key, so revoking one leaves the others working.
* Keys have their own rate-limit counter, so an integration exhausting its limit does not block the account's ordinary users.

An account can hold up to 20 keys. Give each one a meaningful name, because the name is what distinguishes them later. Keys can also be created from the web interface, which is the recommended route for a person rather than a program.

{% hint style="danger" %}
Do not publish an API key anywhere. A key can perform almost any action in the account it belongs to. Make API calls over HTTPS only, because the key travels in clear text.
{% endhint %}

For how to send a key with a request, see [Platform authentication](../../authentication.md).

## API key object

{% openapi-schemas spec="navixy-platform" schemas="ApiKey" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/api/key`.

These operations are available only to the master user, and only with a standard session obtained from [`user/auth`](user/README.md#post-user-auth) with a login and password. An API key cannot be used to manage API keys.

***

{% openapi-operation spec="navixy-platform" path="/api/key/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 4 - User or API key not found or session ended, when the `hash` is invalid or belongs to a non-standard session such as another API key.
* 13 - Operation not permitted, when the caller is not a master user.
* 268 - Over quota, when the account already holds the maximum of 20 API keys.

***

{% openapi-operation spec="navixy-platform" path="/api/key/delete" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 4 - User or API key not found or session ended, when the `hash` is invalid or belongs to a non-standard session such as another API key.
* 13 - Operation not permitted, when the caller is not a master user.
* 201 - Not found in the database, when no key has the given hash.

***

{% openapi-operation spec="navixy-platform" path="/api/key/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 4 - User or API key not found or session ended, when the `hash` is invalid or belongs to a non-standard session such as another API key.
* 13 - Operation not permitted, when the caller is not a master user.
