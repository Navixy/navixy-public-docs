---
description: >-
  How the Analytics API reports errors: the error format, the codes that both
  the Reports and the Dashboards APIs return, and how to handle and retry them.
tags:
  - fundamentals
  - beta
---

# Error handling

This page describes the errors that both the Reports API and the Dashboards API return, and how your app handles them. Each API also has codes of its own, on [Reports API errors](reports/errors.md) and [Dashboards API errors](dashboards/errors.md).

An error has an HTTP status other than 2xx. Except for HTTP 401 and HTTP 406, the response has a JSON body with a `code` that your app checks. Some codes and messages use the word "tool": in them, a tool is an operation of the API.

## Error format

An error body has 3 fields:

| Field | Always present | What it stores |
| --- | --- | --- |
| `code` | Yes | What went wrong, as a fixed value from the tables on this page and on the error page of each API. Your app checks this field |
| `message` | Yes | The error in English, for a person to read. It can change, so your app doesn't check it |
| `details` | No | More about the error, as pairs of names and texts. Only some codes have it |

The following error is a request with an offset in `timeZone`:

```json
{
  "code": "invalid_tool_call",
  "message": "The tool call cannot be run as it is.",
  "details": {
    "timeZone": "the user's IANA time zone, such as Europe/Madrid, not an offset"
  }
}
```

HTTP 401 has no body. Its `WWW-Authenticate` header starts with the word `Bearer`. A request whose `Accept` header excludes JSON gets HTTP 406, also with no body.

## Common error codes

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `invalid_tool_call` | A field of the body is missing or wrong, the body has a field that the operation doesn't accept, or the body isn't a JSON object. `details` names the fields. | Fix the fields that `details` names. See [How to handle a wrong field](#how-to-handle-a-wrong-field). |
| 400 | `bad_request` | The body isn't valid JSON. | Send the body as a JSON object. |
| 401 | None | The request has no token, or the token is invalid or expired. The token of an API key also gets HTTP 401 at any address that isn't an operation of the API, such as the mistyped `/api/tool/context`. | Exchange the API key again and repeat the request with the new token. If a new token gets HTTP 401 too, check the address. |
| 403 | `product_not_enabled` | The workspace doesn't have the product, or the API key's role doesn't include it. | Ask your organization's administrator to add the product to the workspace or to the role. |
| 403 | `insufficient_rights` | The role includes the product, but not its standard feature. The error page of each API names the feature. | Ask your organization's administrator to grant the feature to the role. |
| 403 | `roles_unresolved` | Navixy couldn't read the role of the token. | Contact Navixy support. |
| 403 | `token_incomplete` | The token lacks an ID that the API needs, such as the workspace. `details.claim` names it. | Exchange the API key again. If the error repeats, contact Navixy support. |
| 404 | `not_found` | The address starts with `/api/tools/`, but no operation has this name. | Check the name of the operation. An address without `/api` gets HTTP 405 with an HTML page instead. |
| 405 | `method_not_allowed` | The address exists, but not with this HTTP method. | Send a `POST` request. |
| 406 | None | The `Accept` header of the request excludes JSON. | Accept `application/json`, or send no `Accept` header. |
| 415 | `unsupported_media_type` | The request has a body, but its `Content-Type` is missing or isn't `application/json`. | Send the header `Content-Type: application/json`. |
| 429 | `tool_rate_limited` | The API key has reached the daily limit of the API in this workspace. | Wait until older requests are more than 24 hours old. See [Daily limits](limits.md#daily-limits). |
| 500 | `internal_error` | The request failed in Navixy. | Repeat the request later. If the error repeats, contact Navixy support. |
| 503 | `tool_unavailable` | The API is handling as many requests as it can at once, or a request took too long. | Repeat the request after a short wait. See [How to retry a request](#how-to-retry-a-request). |

A report or a dashboard that the role doesn't grant isn't refused with HTTP 403. The request returns HTTP 400 with `invalid_tool_call`, and `details` names the field, `report` or `dashboard`.

## The order of checks

The API checks a request in this order:

1. The token: HTTP 401.
2. The format of the body: HTTP 400 with `bad_request`, or HTTP 415.
3. The role: HTTP 403.
4. `timeZone`, and whether the body is a JSON object: HTTP 400.
5. The other fields of the body: HTTP 400.
6. The daily limit and the requests that run at once: HTTP 429 or HTTP 503.
7. The data.

A request that fails for several reasons returns the error of the first check that fails.

## How to handle a wrong field

A request with a wrong field returns HTTP 400 with `invalid_tool_call`. Each key of `details` is the name of a field, and its value says what the field must be:

```json
{
  "code": "invalid_tool_call",
  "message": "The tool call cannot be run as it is.",
  "details": {
    "range": "one of 1h, 6h, 12h, 24h, 7d, 14d, 31d"
  }
}
```

To handle the error:

* Fix each field that `details` names, and repeat the request. The value is English text, so log it or show it, but don't parse it.
* Expect a new error after the fix. A wrong `timeZone`, or a body that isn't a JSON object, is reported alone, before the other fields are checked.
* Send only the fields that the operation accepts. A misspelled field returns an error, so it never changes what the operation reads.

## How to retry a request

Some errors are temporary:

* HTTP 503 with `tool_unavailable`: the API is busy, or a request took too long.
* HTTP 500 with `internal_error`.
* An HTTP 5xx error whose body isn't JSON. The Navixy servers end a response that takes too long, before the API answers.

To handle them:

1. Wait a few seconds, then repeat the request.
2. If the error repeats, wait longer before each new attempt.
3. For a slow request, ask for less: a shorter period, fewer assets, or a dashboard with a shorter period.

A request can count toward the daily limit even when it returns an error. See [Daily limits](limits.md#daily-limits).

## See also

* [Reports API errors](reports/errors.md)
* [Dashboards API errors](dashboards/errors.md)
* [Limits](limits.md)
* [Authentication](authentication.md)
