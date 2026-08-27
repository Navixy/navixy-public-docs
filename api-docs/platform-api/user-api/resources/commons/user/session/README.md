---
title: Session
description: Extend the life of the current user session.
---

# Session

A user session obtained from [`user/auth`](../README.md#post-user-auth) expires on an absolute deadline, **30 days after it was issued by default**, and using the session does not push that deadline back. This operation replaces the deadline with a fresh full lifetime starting now, which is how a long-running client stays signed in without asking for credentials again. The period is a per-installation setting: see [how long a session lasts](../../../../authentication.md#how-long-a-session-lasts).

It works only with a standard user session. Calling it with an API key returns error 4, "User or API key not found or session ended", even though the key itself is valid, because an [API key](../../api-keys.md) never expires and has nothing to renew.

## API actions

API base path: `/user/session`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/user/session/renew" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

## More in this section

<!-- endpoint-reference:start -->

#### Delivery session

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/delivery/create`](delivery.md#post-user-session-delivery-create) | POST | Create delivery session |
| [`/user/session/delivery/read`](delivery.md#post-user-session-delivery-read) | POST | Read delivery session |

#### Push token

| Endpoint | Method | What it does |
| --- | --- | --- |
| [`/user/session/push_token/bind`](push_token.md#post-user-session-push_token-bind) | POST | Bind push token |
| [`/user/session/push_token/delete`](push_token.md#post-user-session-push_token-delete) | POST | Delete push token |

<!-- endpoint-reference:end -->
