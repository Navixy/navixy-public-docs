---
description: >-
  How Navixy Repository API authenticates requests: OpenID Connect access
  tokens issued by the Navixy identity service, sent as a bearer token.
---

# Authentication

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Navixy Repository API authenticates every request with an access token. Tokens are issued by the Navixy identity service, built on OpenID Connect (Keycloak), and carry who you are and which workspace you work in. The API itself never sees your password: it only validates tokens.

{% hint style="warning" %}
This page is an early draft. The token-issuing endpoints and the exact sign-in flows will be documented here when the identity setup is finalized.
{% endhint %}

## How authentication works

You authenticate against the identity service and receive an access token. Every API request then carries that token, and the API validates it before executing the operation. A request with a missing, invalid, or expired token fails with an [`UNAUTHORIZED` error](error-handling.md#error-codes).

Two kinds of actors authenticate:

* **Users** sign in with their Navixy account and act through their workspace memberships.
* **Integrations** are service accounts for machine-to-machine access. They authenticate with the OAuth 2.0 client credentials flow (a client ID and secret) and belong to a single workspace.

<!-- TODO: document the token endpoint URL, the sign-in flow for users, and where integration credentials are issued, once the identity setup is finalized -->

## Your workspace ID

The workspace ID that every operation requires is issued when your workspace is provisioned and travels with your credentials: the access token carries your workspace context. You don't discover it through the API.

<!-- TODO: name the exact token claim and the UI location where the workspace ID can be looked up -->

## Using the token in requests

Send the access token with every request in the `Authorization` header:

```
Authorization: Bearer <access token>
```

Tokens expire. Obtain a fresh token before expiry rather than reacting to failures, and treat an [`UNAUTHORIZED` error](error-handling.md#error-codes) on a previously working integration as the signal to refresh.

## See also

* [Error handling](error-handling.md): Understand error structure, codes, and common error scenarios
* [Limits](limits.md): Request, pagination, and input size limits, and the errors they return
