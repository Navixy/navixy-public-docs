---
description: >-
  How to authenticate with Navixy GraphQL API: get an API key, exchange it for
  an access token, send and renew the token, and handle authentication errors.
tags:
  - fundamentals
  - beta
---

# Authentication

This page is for developers who build an app on Navixy GraphQL API and for AI agents that work with it. It covers the whole path from the key to a working request. It also covers what the token contains and the errors that authentication returns. To send your first requests step by step, see [Getting started](../getting-started.md).

Navixy GraphQL API authenticates every request with an access token. Your app gets the token by exchanging an API key at the Navixy identity service and sends the token with every request. Your organization's administrator creates the key in Navixy Console. The API never sees the key itself, only the token.

## How authentication works

An app authenticates in four steps:

1. Your organization's administrator creates an API key in Navixy Console and gives it to you. See [Get an API key](#get-an-api-key).
2. Your app exchanges the key for an access token. If the key's role covers more than one workspace, the app names the workspace. See [Exchange the key for an access token](#exchange-the-key-for-an-access-token).
3. Your app sends the token in the `Authorization` header of every request. See [Send the access token](#send-the-access-token).
4. Before the token expires or when a request gets `UNAUTHORIZED` with HTTP 401, your app exchanges the key again. See [Renew the access token](#renew-the-access-token).

Navixy's own apps get a token of the same kind when a person signs in. The API handles both tokens in the same way.

## Get an API key

An API key is a credential for an app. It starts with `nvx_`, belongs to one organization, and has one role. The role decides what the app can read and change. See [Roles and products](roles-and-products.md). Your organization's administrator creates the key in Navixy Console and chooses its role. To learn how, see [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Navixy Console documentation.

{% hint style="warning" %}
The RESTful [Navixy API](https://navixy.com/docs/navixy-api/) is legacy and has its own API keys. They are different credentials, and Navixy GraphQL API doesn't accept them. A key for Navixy GraphQL API always starts with `nvx_` and comes from Navixy Console.
{% endhint %}

Navixy Console shows the key only once, right after the administrator creates it. Next to the key, Console shows an example request with three values that your app needs:

* The token endpoint, where the app exchanges the key
* The address of the API
* The ID of the workspace where Console was open when the administrator created the key

Ask the administrator for the key and the example request. If you suspect that a key is compromised, ask the administrator to delete it in Navixy Console and create a new key.

## Exchange the key for an access token

To get an access token, send a `POST` request with a form-encoded body to the token endpoint of the Navixy identity service. The exchange follows the [RFC 8693](https://www.rfc-editor.org/rfc/rfc8693.html) standard.

### Request example

The following request exchanges a key for a token that gives access to one workspace:

```bash
curl -L \
  --request POST \
  --url 'https://iam.eu.navixy.com/realms/quickstart/protocol/openid-connect/token' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:token-exchange' \
  --data-urlencode 'client_id=api-key-exchange' \
  --data-urlencode 'subject_token=<API_KEY>' \
  --data-urlencode 'subject_token_type=urn:navixy:token-type:api-key' \
  --data-urlencode 'navixy_agent_name=fleet-report-exporter' \
  --data-urlencode 'navixy_workspace_id=019a6b2f-793e-807b-8001-575350430001'
```

Replace `<API_KEY>` with your whole key. Response:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhY3Rvcl9pZCI6IjAxOWE2YjJmLTc5M2UtODA3Yi04MDAxLTQxNTA0OTRiMDAwMSJ9.c2lnbmF0dXJl",
  "expires_in": 300,
  "token_type": "Bearer",
  "issued_token_type": "urn:ietf:params:oauth:token-type:access_token"
}
```

The response follows the OAuth 2.0 token response and can include more standard fields. Your app needs these fields:

<table><thead><tr><th width="192">Field</th><th>Description</th></tr></thead><tbody><tr><td><code>access_token</code></td><td>The access token. Send it with every request to the API.</td></tr><tr><td><code>expires_in</code></td><td>The number of seconds until the token expires, 300 for every key today. Read it from every response, because the lifetime can change.</td></tr><tr><td><code>token_type</code></td><td>Always <code>Bearer</code>.</td></tr></tbody></table>

A token lasts 5 minutes for every key. The key itself works until its expiration date or its deletion. See [Key deletion and expiration](#key-deletion-and-expiration).

The response has no refresh token. To keep working, exchange the key again as described in [Renew the access token](#renew-the-access-token). When the exchange fails, the response has an error instead of a token. See [Exchange errors](#exchange-errors).

### Token exchange request parameters

The following table lists every parameter of the token exchange request, whether it’s required, and the value to send:

<table data-search="false"><thead><tr><th width="176">Parameter</th><th width="181">Required</th><th>Value</th></tr></thead><tbody><tr><td><code>grant_type</code></td><td>Yes</td><td><code>urn:ietf:params:oauth:grant-type:token-exchange</code></td></tr><tr><td><code>client_id</code></td><td>Yes</td><td><code>api-key-exchange</code>. The exchange needs no client secret, because the key is the credential.</td></tr><tr><td><code>subject_token</code></td><td>Yes</td><td>The API key, including <code>nvx_</code>.</td></tr><tr><td><code>subject_token_type</code></td><td>Yes</td><td><code>urn:navixy:token-type:api-key</code></td></tr><tr><td><code>navixy_agent_name</code></td><td>Yes</td><td>The name of your app, 1 to 100 characters after leading and trailing spaces are removed. The token includes this name, so two apps that share a key can tell their tokens apart.</td></tr><tr><td><code>navixy_workspace_id</code></td><td>When the key's role covers more than one workspace</td><td>The ID of the workspace that the token gives access to. See <a href="#how-to-choose-the-workspace">How to choose the workspace</a>.</td></tr><tr><td><code>navixy_correlation_id</code></td><td>No</td><td>A UUID of your choice. Navixy support can use it to find this exchange in the logs. Without it or with a value that isn't a UUID, the identity service creates one.</td></tr></tbody></table>

### How to choose the workspace

A workspace is a space that holds one customer's data, such as devices, assets, geo objects, and schedules, and has its own set of products. It's created in Navixy Console. A token gives access to one workspace, so the exchange needs to know which workspace your app works with. When the key's role covers exactly one workspace, the exchange uses that workspace, and you can leave out `navixy_workspace_id`. When the role covers more than one workspace, send `navixy_workspace_id`. The example request in Navixy Console already has the ID of the workspace where Console was open when the administrator created the key.

If the role covers several workspaces and the request has no `navixy_workspace_id`, the exchange fails with HTTP 400. The response lists the workspaces that you can choose from:

```json
{
  "error": "workspace_selection_required",
  "error_description": "several workspaces are reachable; repeat with navixy_workspace_id",
  "organization": {
    "id": "019a6b2f-793e-807b-8001-4f52474e0001",
    "name": "TransLog GmbH"
  },
  "workspaces": [
    { "id": "019a6b2f-793e-807b-8001-575350430001", "name": "Hamburg depot" },
    { "id": "019a6b2f-793e-807b-8001-575350430002", "name": "Munich depot" }
  ]
}
```

#### How to handle a key that covers several workspaces

When the key's role covers several workspaces and the request has no `navixy_workspace_id`, the identity service returns `workspace_selection_required`. Choose the workspace by its `name` and repeat the exchange with its `id` as `navixy_workspace_id`. Store the ID in your app's settings, so that later exchanges don't need the extra request. If your app can't choose, use the workspace ID from the example request in Navixy Console, or ask your organization's administrator.

To work with another workspace later, exchange the key again with that workspace's ID. Each token gives access only to the workspace that it was issued for, and the key's role must cover that workspace.

## Send the access token

Send the token in the `Authorization` header of every request: the word `Bearer`, a space, and the token. Never send the API key itself in this header. Navixy GraphQL API has two addresses:

<table><thead><tr><th width="281">Address</th><th>Use it for</th></tr></thead><tbody><tr><td><code>https://nvx4.eu.navixy.com/api/graphql</code></td><td>Queries and mutations, sent as <code>POST</code> requests. Subscriptions over server-sent events use this address too.</td></tr><tr><td><code>wss://nvx4.eu.navixy.com/api/graphql/ws</code></td><td>Subscriptions over a WebSocket connection. See <a href="../graphql-basics/#subscriptions">Subscriptions</a>.</td></tr></tbody></table>

The following request reads the ID and the title of the workspace that the token gives access to:

```bash
curl -L \
  --request POST \
  --url 'https://nvx4.eu.navixy.com/api/graphql' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query MyWorkspace { bdr { workspace { id title } } }"
  }'
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "id": "019a6b2f-793e-807b-8001-575350430001",
        "title": "FleetOps Ltd"
      }
    }
  }
}
```

The API accepts a request without the `Authorization` header and checks each field of the request on its own. A field that doesn’t need a token, such as `__typename`, returns its value. A field that needs a token, such as `bdr` or `node`, returns an `UNAUTHORIZED` error, and the response has no data for it. The HTTP status stays 200, because the API follows the [GraphQL-over-HTTP specification](https://graphql.github.io/graphql-over-http/draft/): the status describes the request as a whole, and each field reports its own result in `errors`. A token that the API can’t accept is different: the API refuses the whole request with HTTP 401. If every request gets `UNAUTHORIZED` with HTTP 200, check the header first.

With a valid token, your app can read the whole schema of the API through introspection. The schema lists every operation that the API has. The key's role decides which of these operations your app can use. See [GraphQL basics](../graphql-basics/) to learn how to send an introspection query and [Public GraphQL schema](../developer-resources/public-graphql-schema.md) for the schema files.

## Renew the access token

A token issued for an API key can't be refreshed. To keep working, exchange the key again and use the new token. Exchange the key in these cases:

* Most of the time in `expires_in` has passed, for example 80%. Then no request fails because of an expired token.
* A request gets `UNAUTHORIZED` with HTTP 401. Exchange the key once and send the request again. If the request fails with the new token too, stop and check the key.
* A subscription ends with an `UNAUTHORIZED` error, because its token expired. Exchange the key and subscribe again. On a WebSocket connection, send the new token in `connection_init`. See [Subscriptions](../graphql-basics/#subscriptions).

Use one token for many requests until it expires. Don't exchange the key before every request: each exchange is an extra request to the identity service.

## Access token claims

An access token is a signed [JSON Web Token](https://www.rfc-editor.org/rfc/rfc7519.html) issued by the Navixy identity service. It contains claims, which are named values. For example, `exp` is the time when the token expires, and `workspace_id` is the workspace that the token gives access to. The claims decide what the API does with a request. When the API refuses a request, decode your token and compare its claims with the table below. Decode the token on your own computer, for example with a JWT library. Don't paste it into an online decoder.

The API identifies who sends the request by the `actor_id` claim. No other claim decides who sends the request. The API ignores every claim that isn't in the table below.

The following example shows a decoded token issued for an API key. It has only the claims that the API reads and the claims that name the key. The values are examples:

```json
{
  "iss": "<identity service URL>",
  "exp": 1790000000,
  "actor_id": "019a6b2f-793e-807b-8001-4150494b0001",
  "actor_type": "api_key",
  "key_name": "Fleet report exporter",
  "agent_name": "fleet-report-exporter",
  "organization_id": "019a6b2f-793e-807b-8001-4f52474e0001",
  "workspace_id": "019a6b2f-793e-807b-8001-575350430001",
  "role_ids": ["019a6b2f-793e-807b-8001-49524f4c0001"],
  "product_codes": ["bdr", "tracking"]
}
```

The following table lists every claim that the API reads from an access token, whether a request needs it, and what the API does with it:

<table><thead><tr><th width="112">Claim</th><th width="76">Type</th><th width="83">Required</th><th>What the API does with it</th></tr></thead><tbody><tr><td><code>actor_id</code></td><td>ID</td><td>Yes</td><td>Identifies who sends the request: the key's ID or a person's ID in Navixy Console. Without it, every request gets <code>UNAUTHORIZED</code>. <code>me.id</code> returns it. To read the key's role through the <code>iam</code> operations, send this ID as <code>actorId</code>. See <a href="../iam/api-reference/#identifiers">IAM API reference</a>.</td></tr><tr><td><code>workspace_id</code></td><td>ID</td><td>Yes</td><td>The workspace whose data the request reads or changes. Without it, every operation that needs a workspace returns <code>PERMISSION_DENIED</code>.</td></tr><tr><td><code>organization_id</code></td><td>ID</td><td>No</td><td>The organization that owns the workspace. The role applies only in this organization, so without the claim no role applies. The API also sends it to Navixy Console to check the workspace. The <code>console</code> and <code>iam</code> operations require it. See <a href="../console/#who-does-what">Console overview</a> and <a href="../iam/#who-does-what">IAM overview</a>.</td></tr><tr><td><code>role_ids</code></td><td>List of strings</td><td>Yes</td><td>The roles for the workspace. A token issued for an API key has the key's one role. Without roles, the API refuses every field that needs a role. See <a href="roles-and-products.md">Roles and products</a>.</td></tr><tr><td><code>product_codes</code></td><td>List of strings</td><td>Yes</td><td>Your workspace's products. Without the claim, the API refuses the operations of every product. See <a href="roles-and-products.md#your-workspaces-products">Your workspace's products</a>.</td></tr></tbody></table>

A token issued for an API key also has three claims that name the key. `actor_type` is `api_key`. `key_name` is the key's name in Navixy Console. `agent_name` is the `navixy_agent_name` that your app sent. The API ignores `actor_type` and `key_name`, and it records `agent_name` with the changes that the key makes. In the audit log of Business Data Repository, the key appears as an `ApiKey` actor. See [Investigating changes with audit logs](../business-data-repository/guides/investigating-changes-with-audit-logs.md).

## How the API chooses your workspace

Every operation of Business Data Repository and Tracking reads or changes the data of one workspace: the workspace in the token's `workspace_id` claim. The API picks this workspace once for the whole request, before it reads or changes any data. For this reason, no operation accepts a workspace ID.

A token from the exchange always has a `workspace_id` claim, and so does a token that Navixy's own apps get. The API refuses the request in these cases:

* The token has no `workspace_id` claim.
* The `workspace_id` claim isn't a workspace ID, or it's the ID of a workspace in another Navixy region or environment.
* Navixy Console reports that the workspace doesn't belong to the organization in the token's `organization_id` claim, or the API can't reach Console to check it.
* The workspace was closed, or Navixy Console suspended it.

The API refuses with a `PERMISSION_DENIED` error, and `detail` says why, for example, `the token names no workspace`. Every field of Business Data Repository and Tracking except `me` needs a workspace. The first field of the request that needs a workspace returns this error. The top-level fields, such as `bdr`, can't be `null`, so `data` is `null` for the whole request.

To see the workspace that the token gives access to, query `bdr.workspace`, as in [Send the access token](#send-the-access-token). See [Workspaces](../business-data-repository/api-reference/workspaces.md) to learn about the workspace entity. A key can't list its workspaces through `me`, because `me.organizations` answers only for a person. The `workspaces` list of the `workspace_selection_required` response is the only list of the workspaces that the key can reach. See [How to handle a key that covers several workspaces](#how-to-handle-a-key-that-covers-several-workspaces).

## Key deletion and expiration

A key stops working when it expires, when your organization's administrator deletes it, or when Navixy Console deletes it on its own. Console deletes a key when the key's role is deleted or when the roles of the administrator who created the key change in any way. An exchange with such a key fails with `invalid_grant`. A token that your app got before the key was deleted keeps working until it expires, with one exception: when the key's role was deleted, the token loses the role's access rights, usually within seconds. No token keeps working after the key's expiration date. See [How to handle a deleted or expired key](#how-to-handle-a-deleted-or-expired-key). To learn when Console deletes a key, see [When a key stops working](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys#when-a-key-stops-working) in the Navixy Console documentation.

## Authentication errors

Authentication errors come from two places. The identity service returns the errors of the exchange. The API returns the errors of the requests that your app sends with the token.

### Exchange errors

The token endpoint returns errors in the format of the OAuth 2.0 standard, [RFC 6749, section 5.2](https://www.rfc-editor.org/rfc/rfc6749.html#section-5.2): a JSON object with `error` and `error_description`.

<table><thead><tr><th width="87">HTTP status</th><th width="101">Error</th><th width="173">Error description</th><th>Cause</th><th>What to do</th></tr></thead><tbody><tr><td>401</td><td><code>invalid_grant</code></td><td><code>api_key_invalid</code></td><td>The key is unknown or incomplete, or it isn't active yet.</td><td>Check that you send the whole key, including <code>nvx_</code>. If the key is correct, ask your organization's administrator whether the key still exists in Navixy Console.</td></tr><tr><td>401</td><td><code>invalid_grant</code></td><td><code>api_key_revoked</code></td><td>The key was deleted.</td><td>Get a new key. See <a href="#how-to-handle-a-deleted-or-expired-key">How to handle a deleted or expired key</a>.</td></tr><tr><td>401</td><td><code>invalid_grant</code></td><td><code>api_key_expired</code></td><td>The key's expiration date has passed.</td><td>Get a new key. See <a href="#how-to-handle-a-deleted-or-expired-key">How to handle a deleted or expired key</a>.</td></tr><tr><td>400</td><td><code>invalid_request</code></td><td><code>agent_name_invalid</code></td><td><code>navixy_agent_name</code> is missing, is empty, or is longer than 100 characters.</td><td>Send a name of 1 to 100 characters.</td></tr><tr><td>400</td><td><code>invalid_request</code></td><td><code>workspace_id_invalid</code></td><td><code>navixy_workspace_id</code> isn't a workspace ID.</td><td>Send an ID from the <code>workspaces</code> list of <code>workspace_selection_required</code>.</td></tr><tr><td>400</td><td><code>workspace_selection_required</code></td><td><code>several workspaces are reachable; repeat with navixy_workspace_id</code></td><td>The key's role covers several workspaces, and the request has no <code>navixy_workspace_id</code>.</td><td>Repeat the exchange with a workspace ID. See <a href="#how-to-handle-a-key-that-covers-several-workspaces">How to handle a key that covers several workspaces</a>.</td></tr><tr><td>403</td><td><code>access_denied</code></td><td><code>workspace_not_in_key_role</code></td><td>The key's role doesn't cover the workspace in <code>navixy_workspace_id</code>, or it covers no workspace.</td><td>Send a workspace that the role covers. If your app needs this workspace, ask your organization's administrator for a key with a role that covers it.</td></tr><tr><td>503</td><td><code>temporarily_unavailable</code></td><td><code>try again</code></td><td>A Navixy service didn't respond.</td><td>Wait a few seconds and exchange the key again.</td></tr><tr><td>500</td><td><code>server_error</code></td><td>The reason</td><td>The identity service failed.</td><td>Try again later. Contact Navixy if the error persists.</td></tr></tbody></table>

If the identity service returns any other error, check that `client_id` is `api-key-exchange` and `subject_token_type` is `urn:navixy:token-type:api-key`. Another client or token type starts another kind of exchange, which doesn't accept API keys.

The following example is the response for a deleted key:

```json
{
  "error": "invalid_grant",
  "error_description": "api_key_revoked"
}
```

#### How to handle a deleted or expired key

Stop exchanging the key and ask your organization's administrator for a new key. Retrying doesn't help, because nobody can restore a deleted or expired key. The administrator creates the new key in Navixy Console. Put the new key into your app's settings. For this reason, your app must read the key from a setting and accept a new key without a code change.

### API errors

The API returns authentication errors in the same format as every other error. See [Error handling](../error-handling.md) for the response structure. In the error, `extensions.status` is 401 for `UNAUTHORIZED` and 403 for `PERMISSION_DENIED`. The HTTP status of the response is in the table.

<table><thead><tr><th>Cause</th><th width="130">Code</th><th width="107">HTTP status</th><th>Details</th><th>What to do</th></tr></thead><tbody><tr><td>The request has no <code>Authorization</code> header. Every field that needs a token, such as <code>bdr</code>, returns the error, and the response has no data for it.</td><td><code>UNAUTHORIZED</code></td><td>200</td><td>"Authentication required"</td><td>Add the header. See <a href="#send-the-access-token">Send the access token</a>.</td></tr><tr><td>The <code>Authorization</code> header has the API key instead of an access token.</td><td><code>UNAUTHORIZED</code></td><td>401</td><td>"The Authorization header holds an API key, not an access token. Exchange the key for an access token and send Authorization: Bearer &#x3C;access token>."</td><td>Exchange the key for an access token and send the token. See <a href="#exchange-the-key-for-an-access-token">Exchange the key for an access token</a>.</td></tr><tr><td>The token is malformed or expired, was issued by another identity service, or is for another service.</td><td><code>UNAUTHORIZED</code></td><td>401</td><td>The reason</td><td>Exchange the key again and send the request once more. If the new token fails too, check that your app sends the exchange to the token endpoint in <a href="#exchange-the-key-for-an-access-token">Exchange the key for an access token</a>.</td></tr><tr><td>The token is valid, but it has no <code>actor_id</code> claim, or the claim names neither an API key nor a person.</td><td><code>UNAUTHORIZED</code></td><td>401</td><td>"Authenticated principal could not be resolved to an actor"</td><td>Exchange the key again. Contact Navixy if the new token fails too.</td></tr><tr><td>The request has no token, and it asks for the schema through introspection.</td><td><code>UNAUTHORIZED</code></td><td>200</td><td>"Introspection requires authentication"</td><td>Send the token. Introspection needs a token like any other query.</td></tr><tr><td>The API can't choose the workspace of the request. Every field of Business Data Repository and Tracking returns the error.</td><td><code>PERMISSION_DENIED</code></td><td>200</td><td>The reason</td><td>Read <code>detail</code>. See <a href="#how-the-api-chooses-your-workspace">How the API chooses your workspace</a>.</td></tr><tr><td>Your workspace doesn't have the product that the request uses.</td><td><code>PRODUCT_DISABLED</code></td><td>200</td><td>"Product tracking is not enabled for the caller's workspace", where <code>tracking</code> is the product's code</td><td>Enable the product for the workspace in Navixy Console, then exchange the key again. See <a href="roles-and-products.md#your-workspaces-products">Your workspace's products</a>.</td></tr><tr><td>The key's role or your workspace's products don't allow a mutation or a field that can't be <code>null</code>.</td><td><code>PERMISSION_DENIED</code></td><td>200</td><td>"Access Denied"</td><td>Ask your organization's administrator for a key with a role that grants the action. See <a href="roles-and-products.md">Roles and products</a>.</td></tr></tbody></table>

A response with HTTP 401 has no `data` field. A `PERMISSION_DENIED` response has one. The following example is the response to a query of `bdr.workspace` with a token that has no `workspace_id` claim:

```json
{
  "errors": [{
    "message": "the token names no workspace",
    "path": ["bdr", "workspace"],
    "extensions": {
      "type": "https://api.navixy.com/errors/forbidden",
      "title": "Permission Denied",
      "status": 403,
      "detail": "the token names no workspace",
      "instance": "/graphql",
      "code": "PERMISSION_DENIED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-21T14:30:00Z"
    }
  }],
  "data": null
}
```

## Best practices

* Keep the API key in a secrets store or an environment variable. Never log the key or a token and never commit either to a repository. A token gives access until it expires, and the key gives access until an administrator deletes it.
* Give each app its own key. Your organization's administrator can then delete one app's key without stopping the others, and Navixy Console shows when each key last signed in. The request rate and the number of open subscriptions are limited per key, so apps that share a key share these limits. See [Limits](../limits.md).
* Get the key from an administrator who stays with your organization and whose roles rarely change. The reason is that Navixy Console deletes a key when its creator leaves the organization or when their roles change, and your app stops until it gets a new key. See [Key deletion and expiration](#key-deletion-and-expiration).
* Give each copy of an app its own `navixy_agent_name`, so that you can tell their tokens apart. See [Exchange the key for an access token](#exchange-the-key-for-an-access-token).
* Ask for a key with the narrowest role that your app needs. Every request from the app has the access of that role.
* Read `extensions.code`, not the HTTP status, to tell an expired token from a refused operation. Only `UNAUTHORIZED` (401), `RATE_LIMITED` (429), and a request that the server can't run at all change the HTTP status. See [HTTP status codes](../error-handling.md#http-status-codes).

## See also

- [Getting started](../getting-started.md): Exchange a key, send your first requests, and activate a GPS device.
- [Roles and products](roles-and-products.md): Learn what the key's role and your workspace's products allow.
- [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in Navixy Console: Read how administrators create and delete keys.
- [Error handling](../error-handling.md): See the error format and every error code.

