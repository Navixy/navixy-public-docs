---
description: >-
  How Navixy Repository API authenticates requests: OpenID Connect access tokens
  from the Navixy identity service, how a product obtains one for its users, what
  the token contains, and how to send it.
hidden: true
---

# Authentication

{% include ".gitbook/includes/navixy-repository-api-is-a-....md" %}

Navixy Repository API authenticates every request with an access token. The token is an [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) access token issued by the Navixy identity service. The API never sees your password or client secret: it only validates the token and identifies the caller from it.

This page is for developers who integrate with Navixy Repository API directly or through Navixy SDK. It covers how a product obtains a token for its users, what the token contains, how to send it, and the errors that authentication returns. It describes what works today. Machine-to-machine credentials for integrations aren't available yet, and the page says so where it matters.

## How authentication works

You obtain an access token from the Navixy identity service and send it with every request in the `Authorization` header. The API validates the signature and the issuer of the token, then runs the operation as the user that the token identifies. A request with a missing, invalid, or expired token fails with an [`UNAUTHORIZED` error](error-handling.md#error-codes) and HTTP status 401.

Every query and mutation that lists or creates entities takes a `workspaceId` argument. A workspace is the tenant that owns your devices, assets, geo objects, and schedules. An organization is the Navixy Console account that owns one or more workspaces. See [Key concepts](./#key-concepts) for the full domain model and [Workspaces](workspaces/) for the entity. The token identifies who is calling, and the `workspaceId` argument says which workspace the call works in.

Today only users authenticate. A user is a person with a Navixy account, and a user can belong to several organizations and workspaces. Integrations, the service accounts for machine-to-machine access, are part of the domain model but can't obtain credentials yet. See [Integrations: not available yet](#integrations-not-available-yet).

## How to obtain a token

A token comes from the sign-in flow of a product: a web or mobile app with its own front end, whose users sign in with their Navixy account. Navixy provisions the identity clients for each product. There is no self-service way to create them, and no way to obtain a token without a product. Values in angle brackets, such as `<token endpoint URL>`, are the ones that Navixy provides with the clients.

### Sign in users and exchange the session token

Use this flow when you want people to sign in to your own web or mobile app with their Navixy account. It has two steps. The browser signs in and receives a session token: a token that proves who signed in. Your backend then exchanges the session token for a context token: a token that also names the organization and workspace to work in. The context token is what your code sends to Navixy Repository API.

A session token doesn't name an organization or a workspace. Only the exchange step adds that information, and only a confidential client that your backend controls may perform the exchange. The exchange secret therefore never has to be sent to the browser.

Before you start, request two clients in the Navixy identity service from Navixy: a public login client for your front end and a confidential exchange client for your backend. You receive the client IDs and the secret of the exchange client. Store the secret as a deployment secret of your backend. Never send it to the browser or commit it to a repository.

#### Step 1: Sign the user in

Sign the user in with the standard OpenID Connect [authorization code flow](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) with [PKCE](https://www.rfc-editor.org/rfc/rfc7636.html) against your login client. No Navixy-specific flow is needed, so any OpenID Connect library works. Keep the resulting session token in the browser and renew it in the background, without showing the sign-in page again. The session token authenticates the user to your backend for one purpose: being exchanged.

If the user has no Navixy account, the sign-in page offers self-registration. Registration creates the user, an organization, and a workspace, and gives the user the owner role in that workspace.

#### Step 2: Exchange the session token for a context token

From your backend, send a token exchange request to the token endpoint of the identity service. The request follows [RFC 8693, OAuth 2.0 Token Exchange](https://www.rfc-editor.org/rfc/rfc8693.html), with three Navixy-specific parameters added. The body is form-encoded:

```bash
curl -L \
  --request POST \
  --url '<token endpoint URL>' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:token-exchange' \
  --data-urlencode 'client_id=<your exchange client ID>' \
  --data-urlencode 'client_secret=<your exchange client secret>' \
  --data-urlencode 'subject_token=<session token from the Authorization header of the incoming request>' \
  --data-urlencode 'subject_token_type=urn:ietf:params:oauth:token-type:access_token' \
  --data-urlencode 'navixy_organization_id=019d48ea-0752-8000-801f-444556437ab0' \
  --data-urlencode 'navixy_workspace_id=019d48ea-0752-8000-801f-444556437ab1' \
  --data-urlencode 'navixy_correlation_id=6f1d2c3b-9a8e-4f70-b1c2-0d3e4f5a6b7c'
```

The response is a standard [OAuth 2.0 token response](https://www.rfc-editor.org/rfc/rfc6749.html#section-5.1). The value of `expires_in` depends on the configuration of the identity service:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6...",
  "expires_in": 300,
  "token_type": "Bearer",
  "scope": "openid"
}
```

**Parameters explained:**

* `grant_type`, `subject_token`, and `subject_token_type` are the standard [RFC 8693 request parameters](https://www.rfc-editor.org/rfc/rfc8693.html#section-2.1). `subject_token` is the session token of the signed-in user.
* `client_id` and `client_secret` identify your exchange client. Only this client may exchange, and only your backend holds its secret.
* `navixy_organization_id` and `navixy_workspace_id` (optional) name the organization and workspace that the user selected. Leave both out to let the identity service choose. A workspace without its organization is rejected, because workspace IDs are unique only within an organization.
* `navixy_correlation_id` (required) is a UUID that your backend generates for each request. The identity service uses it to match the request with its own records while it creates the token, then discards it. It never becomes a claim.

The organization and workspace parameters are a preference, not a command. If the user no longer has access to the organization or workspace that you named, the identity service ignores that value. It picks one that the user can access instead. If you name nothing, the identity service picks the organization with the lowest ID. Inside it, it picks the workspace with the lowest ID that the roles of the user include. The same user with the same roles always gets the same result, so a sign-in is repeatable.

The exchange returns either a complete token or an error. A missing correlation ID, a request for an organization or workspace that the user can't access, or an unreachable identity backend all return an error. You never receive a token without an organization and a workspace. See [Authentication errors](#authentication-errors).

Refresh is re-exchange. No refresh token is issued for context tokens. Store `expires_in` next to the token and exchange the session token again shortly before the access token expires. When the sign-in session of the user has ended, the exchange fails, and the correct outcome is to show the sign-in page again.

### Integrations: not available yet

An integration is a service account for a server-side job or service that calls the API without a person present: a synchronization job, an ERP connector, a reporting script. The identity service doesn't issue credentials for integrations yet, so there is no way to obtain a token for one. This page is updated when that changes.

Navixy SDK already has a `clientCredentials` helper for the OAuth 2.0 client credentials grant, so code written against it doesn't change when credentials become available.

## What the token contains

The access token is a signed [JSON Web Token](https://www.rfc-editor.org/rfc/rfc7519.html). It has the standard claims `iss`, `sub`, `exp`, and `azp`. A context token from the exchange in [Sign in users and exchange the session token](#sign-in-users-and-exchange-the-session-token) also contains the claims below. Read them to learn which workspace the token was issued for and which roles the user holds.

Navixy Repository API uses the token to identify the caller. It doesn't read the workspace or role claims: the workspace comes from the `workspaceId` argument of each operation. Don't build authorization on the claims in your own code either. Treat them as information for your app, such as which workspace to pass and whether to show an organization picker.

| Claim                      | Type              | Meaning                                                                                                             |
| -------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------- |
| `actor_id`                 | UUID              | The actor that the token represents. An actor is any party that a token can represent: today, a user.              |
| `actor_type`               | String            | One of `user`, `integration`, `ai_agent`, or `external_contractor`. Today always `user`.                             |
| `organization_id`          | UUID              | The organization that the token was issued for.                                                                     |
| `workspace_id`             | UUID              | The workspace that the token was issued for. Pass this value as `workspaceId` in queries and mutations.             |
| `role_ids`                 | List of UUIDs     | The roles that the user holds in this organization and workspace. Roles from other workspaces are absent.           |
| `many_organizations`       | Boolean           | Whether the user can access more than one organization. Use it to decide whether to show an organization switcher.  |
| `many_workspaces`          | Boolean           | Whether the user can access more than one workspace inside this organization.                                       |
| `session_ttl`, `token_ttl` | ISO 8601 duration | How long the sign-in session and the token are meant to last, for example `PT12H`.                                  |
| `refresh`                  | Boolean           | Whether refresh is allowed for this user.                                                                           |
| `min_acr`                  | `1` or `2`        | The minimum authentication level: `1` for a single factor, `2` for multi-factor authentication.                     |
| `write`                    | String            | The rule for write operations: `role`, `fresh`, or `fresh+coapproval`.                                              |
| `max_age`                  | ISO 8601 duration | Present only when write operations require a recent sign-in.                                                        |

A decoded payload looks like this:

```json
{
  "iss": "<issuer URL>",
  "sub": "f:3b2a1c0d-1111-4222-8333-944455566677:user@example.com",
  "exp": 1757000300,
  "actor_id": "019d48ea-0752-8000-801f-444556437aa9",
  "actor_type": "user",
  "organization_id": "019d48ea-0752-8000-801f-444556437ab0",
  "workspace_id": "019d48ea-0752-8000-801f-444556437ab1",
  "role_ids": ["019d48ea-0752-8000-801f-000000000101"],
  "many_organizations": false,
  "many_workspaces": true,
  "session_ttl": "PT12H",
  "token_ttl": "PT5M",
  "refresh": true,
  "min_acr": 1,
  "write": "role"
}
```

A session token, the one the browser receives before the exchange, contains only the standard claims. It identifies the user, and the API accepts it today. It has no organization, workspace, or role claims, so your app has to know the workspace ID from elsewhere. See [Your workspace ID](#your-workspace-id).

## Using the token in requests

Send the access token in the `Authorization` header with the `Bearer` scheme. The header is the only supported way to send the token: the API doesn't read tokens from the query string or the request body. `<API endpoint URL>` stands for the GraphQL endpoint of Navixy Repository API, which Navixy provides together with your credentials.

```bash
curl -L \
  --request POST \
  --url '<API endpoint URL>' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'Content-Type: application/json' \
  --data '{
    "query": "query MyWorkspace($id: ID!) { bdr { workspace(id: $id) { id title isActive } } }",
    "variables": { "id": "019d48ea-0752-8000-801f-444556437ab1" }
  }'
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspace": {
        "id": "019d48ea-0752-8000-801f-444556437ab1",
        "title": "Hamburg logistics",
        "isActive": true
      }
    }
  }
}
```

## Your workspace ID

Every query and mutation that lists or creates entities takes a `workspaceId` argument. A workspace is the tenant that owns your devices, assets, geo objects, and schedules. To find the ID, list the workspaces that your token can access:

```graphql
query MyWorkspaces {
  bdr {
    workspaces(first: 10) {
      nodes {
        id
        title
        isActive
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "workspaces": {
        "nodes": [
          {
            "id": "019d48ea-0752-8000-801f-444556437ab1",
            "title": "Hamburg logistics",
            "isActive": true
          }
        ]
      }
    }
  }
}
```

A context token also states the workspace in its `workspace_id` claim, and that value is the same ID. Workspaces are read-only in Navixy Repository API. They are created, renamed, and closed in Navixy Console. See [Workspaces](workspaces/).

## Recommended authentication flow

For an app with its own front end, the flow is:

1. Sign the user in with OpenID Connect and PKCE against your login client, and keep the session token in the browser.
2. On each request to your backend, exchange the session token for a context token, or reuse a cached context token that hasn't expired.
3. Send the context token to Navixy Repository API in the `Authorization` header, with the `workspace_id` claim as the `workspaceId` argument.
4. Re-exchange shortly before `expires_in` runs out. When the exchange fails because the sign-in session ended, show the sign-in page.

Navixy SDK sends the token with every request and calls your token provider before each one. Steps 3 and 4 therefore need no code of your own beyond the provider.

## Authentication errors

Authentication failures return a GraphQL error envelope with [RFC 9457](https://www.rfc-editor.org/rfc/rfc9457.html) extensions, the same format as every other error of the API. Two codes come from authentication:

| Code                | HTTP status | Description                                                                                | How to resolve                                                                                                                  |
| ------------------- | ----------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `UNAUTHORIZED`      | 401         | The token is missing, malformed, expired, or wasn't issued by the Navixy identity service. | Obtain a new token and retry. If a working app starts receiving this error, its token expired.                                  |
| `PERMISSION_DENIED` | 403         | The token is valid, but the user lacks the permission for this operation.                  | The operation isn't allowed for this account. Permissions are managed outside Navixy Repository API, so ask your administrator. |

Token endpoint failures come from the identity service in the standard [OAuth 2.0 error format](https://www.rfc-editor.org/rfc/rfc6749.html#section-5.2). The body has `error` and `error_description` fields, and the HTTP status is 400 or 401. A failed exchange means that no token was issued, never a token with reduced content.

### Unauthorized (401)

Returned before the query runs, so the response has no `data` field and the HTTP status is 401. This example shows an expired token:

```json
{
  "errors": [{
    "message": "Jwt expired at 2026-09-02T09:05:00Z",
    "extensions": {
      "type": "https://api.navixy.com/errors/unauthorized",
      "title": "Unauthorized",
      "status": 401,
      "detail": "Jwt expired at 2026-09-02T09:05:00Z",
      "instance": "/graphql",
      "code": "UNAUTHORIZED",
      "traceId": "0af7651916cd43dd8448eb211c80319c",
      "timestamp": "2026-09-02T09:07:12.000Z"
    }
  }]
}
```

#### How to handle expired or invalid tokens

Obtain a new token through the exchange, then retry the request once. Don't retry the same token: the identity service doesn't extend a token on failure, and a retry loop with an expired token only adds delay. Refresh on a schedule instead, shortly before `expires_in` runs out, so a working app never sees this error.

### Token exchange failure (400)

Returned by the token endpoint when the exchange can't be completed. Typical causes are an expired session token and a requested workspace that isn't available to the user. The `error_description` text comes from the identity service and varies by cause:

```json
{
  "error": "invalid_grant",
  "error_description": "Invalid token"
}
```

#### How to handle token exchange failures

Check the session token first. If it expired, the user has to sign in again. If the session token is valid, drop the `navixy_organization_id` and `navixy_workspace_id` parameters and exchange again. The identity service then picks an organization and a workspace that the user can access. A failure without any selection means that the user has no role in any organization.

For the `PERMISSION_DENIED` error and the full error format, see [Error handling](error-handling.md).

## Best practices

* Keep the exchange secret on the server. A secret in a browser bundle or a repository is public.
* Refresh before expiry, not after failure. Store `expires_in` with the token and exchange again a little early.
* Reuse one token across requests. Exchanging for every API call multiplies the load on the identity service and adds an extra HTTP request to every API call.
* Read `workspace_id` from the context token instead of writing a fixed workspace ID into your code. A fixed ID breaks when the same code runs for a user in another workspace.
* Handle `UNAUTHORIZED` and `PERMISSION_DENIED` differently. The first means "get a new token". The second means "this account can't do this", and a new token doesn't change that.
* Treat the token as opaque except for the documented claims. Claim names outside the table above can change without notice.

## See also

* [Getting started](getting-started.md): Make your first query and mutation with a token.
* [Error handling](error-handling.md): The error envelope, all error codes, and how to handle each one.
* [Limits](limits.md): The request rate limit that applies per authenticated caller.
* [Workspaces](workspaces/): The workspace entity and the queries that list its contents.
