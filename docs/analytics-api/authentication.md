---
description: >-
  How to authenticate with the Analytics API: exchange a Navixy API key for an
  access token and send the token to the Reports and Dashboards APIs.
tags:
  - fundamentals
  - beta
---

# Authentication

The Reports API and the Dashboards API authenticate every request with an access token. It's the same token that Navixy GraphQL API accepts. Your app gets it in the same way: by exchanging an API key at the Navixy identity service. An app that already works with Navixy GraphQL API can send its token to the Analytics API without changes.

## How authentication works

An app authenticates in 4 steps:

1. Your organization's administrator creates an API key in Navixy Console and gives it to you. The key's role decides which reports and dashboards your app can read. See [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Navixy Console documentation.
2. Your app exchanges the key for an access token. See [Exchange the key for an access token](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#exchange-the-key-for-an-access-token) in the Navixy GraphQL API documentation.
3. Your app sends the token in the `Authorization` header of every request. See [Send the access token](#send-the-access-token).
4. The token expires after the number of seconds in `expires_in`, 300 today. Before it expires, or when a request returns HTTP 401, your app exchanges the key again. See [Renew the access token](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#renew-the-access-token).

A token gives access to one workspace. If the key's role covers several workspaces, name the workspace in the exchange with `navixy_workspace_id`. See [How to choose the workspace](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#how-to-choose-the-workspace).

## Send the access token

Send the token in the `Authorization` header: the word `Bearer`, a space, and the token. Never send the API key itself in this header. Replace `<REGION>` with the region of your organization, the same region as the identity service that issued the token.

The following request reads the report types and the assets that the token gives access to in Reports:

```bash
curl --request POST \
  --url 'https://reports.<REGION>.navixy.com/api/tools/context' \
  --header "Authorization: Bearer $ACCESS_TOKEN" \
  --header 'Content-Type: application/json' \
  --data '{"timeZone": "Europe/Madrid"}'
```

The same token works for the Dashboards API at `https://dashboards.<REGION>.navixy.com/api`.

## Authentication errors

<table><thead><tr><th width="120">HTTP status</th><th>When it happens</th><th>What to do</th></tr></thead><tbody><tr><td>401</td><td>The request has no token, or the token is invalid or expired. A valid token also gets 401 at an address that isn't an operation of the API, for example a mistyped path.</td><td>Exchange the key again and repeat the request with the new token. If a new token gets 401 too, check the address of the request.</td></tr><tr><td>403</td><td>The token is valid, but the workspace doesn't have the product, or the key's role doesn't include the product. For a report or dashboard that the role doesn't grant, see <a href="error-handling.md#common-error-codes">Common error codes</a>.</td><td>Ask your organization's administrator to add the product to the workspace or to give the key a role with the access that your app needs.</td></tr></tbody></table>

For every error code of the Analytics API, see [Error handling](error-handling.md).
