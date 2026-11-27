---
description: >-
  How to authenticate with the Analytics API: a session token from a signed-in
  browser for the API, and an access token from an API key for the tools.
tags:
  - fundamentals
  - beta
---

# Authentication

The Analytics API has 2 approaches, and each one authenticates in its own way. Every request sends a token in the `Authorization` header: the word `Bearer`, a space, and the token. This page describes how to get the token of each approach.

| | API | Tools |
| --- | --- | --- |
| Token | The session token of a person who is signed in to Reports or Dashboards | An access token that your app gets from a Navixy Console API key |
| How you get it | Copy it from the signed-in browser | Exchange the API key at the Navixy identity service |
| Lifetime | 5 minutes. The page renews it while it's open | 5 minutes. Your app exchanges the key again |
| Use it for | Your own requests and scripts that you run while you work | Integrations and AI agents that run on their own |
| Paths | `/api/reports`, `/api/assets`, `/api/dashboards`, `/api/me/permissions` | `/api/tools/` |

Each token gives access to one workspace. The response has the data of that workspace only. The role decides which reports and dashboards the token can read: the role of the person who is signed in, or the role of the API key. See [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles) in the Navixy Console documentation.

## API access

The API accepts the session token of a person who is signed in to Navixy Reports or Navixy Dashboards. The page of the product gets this token when you sign in, and sends it with each of its own requests. You copy it from there and send it with your requests.

### Copy the session token

To copy the session token, follow these steps:

1. Open Reports at `https://reports.<REGION>.navixy.com` or Dashboards at `https://dashboards.<REGION>.navixy.com`, and sign in. Replace `<REGION>` with `eu` or `us`, the region of your organization.
2. Choose the organization and the workspace whose data you want to read. The token gives access to this workspace.
3. Open the developer tools of your browser, and open the **Network** tab.
4. Reload the page, and select a request to `/api/reports` or `/api/me/permissions` in Reports, or to `/api/dashboards` in Dashboards.
5. In the request headers, copy the value of `Authorization` after the word `Bearer` and the space.

Don't copy the token of the request to `/api/auth/exchange`. That request sends the token of your sign-in, which the API refuses with HTTP 401.

A token of Reports works for the Reports API, and a token of Dashboards works for the Dashboards API.

### Send the session token

The following request lists the report types that the session token can read:

```bash
curl --request GET \
  --url 'https://reports.<REGION>.navixy.com/api/reports' \
  --header "Authorization: Bearer $SESSION_TOKEN"
```

### Session token lifetime

A session token expires 5 minutes after the page got it. While the page stays open, it gets a new token before the old one expires. When a request returns HTTP 401, copy the token again from a newer request of the page. When you sign out or close the page, the page stops renewing the token.

Use the session token for requests that you make while you work, such as reading a report for a script or a spreadsheet. For an integration that runs on its own, use the tools with an API key. See [Tools access](#tools-access).

## Tools access

The tools accept the access token that your app gets from a Navixy Console API key. It's the same token that Navixy GraphQL API accepts. An app that works with Navixy GraphQL API can send its token to the tools without changes.

An app authenticates in 4 steps:

1. Your organization's administrator creates an API key in Navixy Console and gives it to you. The key's role decides which reports and dashboards your app can read. See [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Navixy Console documentation.
2. Your app exchanges the key for an access token. See [Exchange the key for an access token](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#exchange-the-key-for-an-access-token) in the Navixy GraphQL API documentation.
3. Your app sends the token in the `Authorization` header of every request. See [Send the access token](#send-the-access-token).
4. The token expires after the number of seconds in `expires_in`, about 300 seconds (5 minutes). Before it expires, or when a request returns HTTP 401, your app exchanges the key again. See [Renew the access token](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#renew-the-access-token).

A token gives access to one workspace. If the key's role covers several workspaces, name the workspace in the exchange with `navixy_workspace_id`. See [How to choose the workspace](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#how-to-choose-the-workspace).

### Send the access token

Send the token in the `Authorization` header. Never send the API key itself in this header. Replace `<REGION>` with the region of your organization, the same region as the identity service that issued the token.

The following request reads the report types and the assets that the token gives access to in Reports:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/context' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Madrid"}'
```

The same token works for the Dashboards tools at `https://dashboards.<REGION>.navixy.com/api/tools/`.

The tools of each product also accept the session token of that product, so you can try them from a signed-in browser session. The API doesn't accept the access token of an API key.

## Authentication errors

<table><thead><tr><th width="120">HTTP status</th><th>When it happens</th><th>What to do</th></tr></thead><tbody><tr><td>401</td><td>The request has no token, or the token is invalid or expired. The access token of an API key also gets HTTP 401 on every path outside `/api/tools/`, including a mistyped one.</td><td>Get a new token and repeat the request. If a new token gets HTTP 401 too, check the path: an API key works only with the tools.</td></tr><tr><td>403</td><td>The token is valid, but the workspace doesn't have the product, or the role doesn't include it.</td><td>Ask your organization's administrator to add the product to the workspace or to the role.</td></tr></tbody></table>

For every error code of the Analytics API, see [Error handling](error-handling.md).
