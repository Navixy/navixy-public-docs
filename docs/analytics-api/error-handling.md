---
description: >-
  How the Analytics API reports errors: the error format, the codes that the
  API and the tools return, and how to handle and retry them.
tags:
  - fundamentals
  - beta
---

# Error handling

This page describes the errors that both approaches of the Analytics API return, the API and the tools, and how your app handles them. Each product also has codes of its own, on [Reports errors](reports/errors.md) and [Dashboards errors](dashboards/errors.md).

An error has an HTTP status other than 2xx. Except for HTTP 401 and HTTP 406, the response has a JSON body with a `code` that your app checks.

## Error format

The API and the tools return errors in the same format. An error body has 3 fields:

| Field | Always present | What it stores |
| --- | --- | --- |
| `code` | Yes | What went wrong, as a fixed value from the tables on this page and on the error page of each product. Your app checks this field |
| `message` | Yes | The error in English, for a person to read. It can change, so your app doesn't check it |
| `details` | No | More about the error, as pairs of names and texts. Only some codes have it |

The following error is a tool request with an offset in `timeZone`:

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

The API and the tools return these codes:

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `bad_request` | The body isn't valid JSON. | Send the body as a JSON object. |
| 401 | None | The request has no token, or the token is invalid or expired. The access token of an API key also gets HTTP 401 on every path outside `/api/tools/`, including a mistyped one. | Get a new token and repeat the request. If a new token gets HTTP 401 too, check the path and the approach. See [Authentication](authentication.md). |
| 403 | `product_not_enabled` | The workspace doesn't have the product, or the role doesn't include it. | Ask your organization's administrator to add the product to the workspace or to the role. |
| 403 | `insufficient_rights` | The role includes the product, but not its standard feature. The error page of each product names the feature. | Ask your organization's administrator to grant the feature to the role. |
| 403 | `roles_unresolved` | Navixy couldn't read the role of the token. | Contact Navixy support. |
| 403 | `token_incomplete` | The token lacks an ID that the product needs, such as the workspace. `details.claim` names it. | Get a new token. If the error repeats, contact Navixy support. |
| 404 | `not_found` | No operation has this address. An address without `/api` gets HTTP 405 with an HTML page instead. | Check the address of the request. |
| 405 | `method_not_allowed` | The address exists, but not with this HTTP method. | Use the method that the reference gives: the tools take `POST`. |
| 406 | None | The `Accept` header of the request excludes JSON. | Accept `application/json`, or send no `Accept` header. |
| 415 | `unsupported_media_type` | The request has a body, but its `Content-Type` is missing or isn't `application/json`. | Send the header `Content-Type: application/json`. |
| 500 | `internal_error` | The request failed in Navixy. | Repeat the request later. If the error repeats, contact Navixy support. |

## Tools error codes

The tools also return these codes:

| HTTP status | Code | When it occurs | How to resolve |
| --- | --- | --- | --- |
| 400 | `invalid_tool_call` | A field of the body is missing or wrong, the body has a field that the tool doesn't accept, or the body isn't a JSON object. `details` names the fields. | Fix the fields that `details` names. See [How to handle a wrong field](#how-to-handle-a-wrong-field). |
| 429 | `tool_rate_limited` | The API key has reached the daily limit of the tools in this workspace. | Wait until older requests are more than 24 hours old. See [Tools limits](limits.md#tools-limits). |
| 503 | `tool_unavailable` | The tools are handling as many requests as they can at once, or a request took too long. | Repeat the request after a short wait. See [How to retry a request](#how-to-retry-a-request). |

The 2 approaches refuse a report or a dashboard that the role doesn't grant in different ways. The API returns HTTP 403. A tool returns HTTP 400 with `invalid_tool_call`, and `details` names the field, `report` or `dashboard`.

## The order of checks

Each request is checked in this order, and a request that fails for several reasons returns the error of the first check that fails:

1. The token: HTTP 401.
2. The format of the body: HTTP 400 with `bad_request`, or HTTP 415.
3. The role, and for the API the report type or dashboard: HTTP 403.
4. For the API, whether the report type or dashboard exists: HTTP 404.
5. The fields of the body or the query: HTTP 400. A tool checks `timeZone` first.
6. For the tools, the daily limit and the requests that run at once: HTTP 429 or HTTP 503.
7. The data.

## How to handle a wrong field

A request with a wrong field returns HTTP 400. The tools return `invalid_tool_call`, and the API returns the code of the product, such as `invalid_report_parameters`. Each key of `details` is the name of a field, and its value says what the field must be:

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
* Expect a new error after the fix. In a tool request, a wrong `timeZone`, or a body that isn't a JSON object, is reported alone, before the other fields are checked.
* Send only the fields that the operation accepts. A misspelled field returns an error, so it never changes what the operation reads.

## How to retry a request

Some errors are temporary:

* HTTP 503: the product is busy or can't read its data at the moment, or a request took too long.
* HTTP 500 with `internal_error`.
* An HTTP 5xx error whose body isn't JSON. The Navixy servers end a response that takes too long, before the API answers.

To handle them:

1. Wait a few seconds, then repeat the request.
2. If the error repeats, wait longer before each new attempt.
3. For a slow request, ask for less: a shorter period, fewer assets, or a dashboard with a shorter period.

A tool request can count toward the daily limit even when it returns an error. See [Tools limits](limits.md#tools-limits).

## See also

* [Reports errors](reports/errors.md)
* [Dashboards errors](dashboards/errors.md)
* [Limits](limits.md)
* [Authentication](authentication.md)
