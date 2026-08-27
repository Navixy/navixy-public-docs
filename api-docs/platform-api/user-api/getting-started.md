---
title: Navixy platform API
description: What the Platform API covers, its core concepts, and how to use this documentation.
---

# Navixy platform API

The Platform API is the whole of what a Navixy user can do, available programmatically. Anything the web interface offers, an integration can do through this API: register and configure tracking devices, read where they have been, define the rules that turn movement into events, assign work to field employees, and bill for it. Requests and responses are JSON over HTTPS.

It is the larger of the two Navixy APIs and the one most integrations need. Its counterpart, the [Admin Panel API](../panel-api/getting-started.md), manages a reseller deployment from above: the accounts, the devices issued to them, and the plans they are on. The two are separate, including their sessions, so a hash from one cannot be used with the other.

Use the API to:

* Register devices, configure them remotely, and send them commands.
* Read tracks, raw points, sensor readings, and generated reports.
* Define geofences and rules, and consume the events they produce.
* Assign tasks and routes to field employees and collect the forms they return.
* Keep vehicle records, maintenance schedules, and driver journals.
* Manage sub-users and the subset of the account each one may see.

## How to use this documentation

This section documents the Platform API in full. Choose the path below depending on who, or what, is reading.

### For developers

Start with [Key concepts](#key-concepts) below to see how trackers, tracks, rules, and users relate, then get access: [Platform authentication](authentication.md) covers obtaining a session hash and, for anything long-lived, an API key.

Once you are authenticated, [Guides and examples](guides/) walks whole tasks end to end, with the calls in order. [Resources](resources/) is the reference: every operation, with its full parameter and response schema and a panel for sending a test request. [Errors](../general/errors.md) carries the error code table shared with the Admin Panel API.

Before your first call, read [Request conventions](#request-conventions) below, which covers how parameters are passed and the rules specific to this section. [API conventions](../general/api-conventions.md) carries what is shared with the Admin Panel API: the base URLs, the data types, the date and time format, and the request rate limits.

### For AI agents and LLMs

* **[OpenAPI specification](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/api-docs/platform-api/user-api/reference/Navixy_Platform.json)**: the raw JSON specification for every endpoint, parameter, and schema. Every resource page renders it directly, so the parameter tables, response schemas, and status codes you see are generated from it rather than written by hand. Treat it as the technical source of truth.
* **[Navixy docs via MCP](https://app.gitbook.com/s/gh5cGQ23uFYTcp7Fj7Yd/using-navixy-documentation-with-ai)**: connect to query this documentation interactively instead of parsing static pages.

## Key concepts

A user owns trackers. Each tracker reports its position over time, which the Navixy platform stores as tracks and as raw points. Rules watch a tracker for a condition and write an event to history when it is met. Everything else hangs off those four.

| Term | What it is |
| --- | --- |
| User | An account on the platform. Authenticating as one returns the session hash that almost every other call needs. |
| Sub-user | An additional login under the same account, with its own rights and its own subset of the account's trackers. |
| Tracker | A device registered to a user, and the record the platform keeps of it. The central entity of this API: most other resources hang off it. |
| Source | The physical device behind a tracker. Several trackers can share one source, which is how two users see the same device. |
| Track | A period of movement the platform detected, with totals. The raw points behind it are read separately. |
| Sensor | A measurement a device reports alongside its position, such as fuel level or temperature. |
| Geofence | A named area on the map, used to limit where a rule applies and to name locations in reports. |
| Rule | A condition watched on a tracker. When it is met, the platform logs an event and can notify someone. |
| History | The record of the events rules produced, which is what a notification feed is built from. |
| Task | A job assigned to a tracked device, completed by visiting a place and meeting its conditions. |
| Session hash | What authenticating returns, valid until it expires or the password changes. An API key replaces it for anything long-lived. |
| API key | A long-lived credential for an integration, used in place of a session hash. An account may hold up to 20. |

## Sections

* [Guides and examples](guides/): whole tasks worked end to end, with the calls in order.
* [Resources](resources/): the operation reference, grouped by resource family.
* [WebSocket](websocket/): a persistent connection that pushes events as they happen, instead of being polled.

Two smaller APIs sit under Resources because they are part of the same section a reader navigates, though they have their own base URLs and their own specifications: [Raw IoT Data API](resources/data-warehouse-api/) for unprocessed device data, and [Eco Fleet API](resources/eco-fleet-api/) for fuel sensor quality.

## Standard workflow

A first request, worked through on a common example: getting the points of a track.

1. **Determine the URL to API calls**:
   * Pick the base URL for the platform you are on, for example `https://api.eu.navixy.com/v2` for Europe. The full list is in [Base URLs](../general/api-conventions.md#base-urls).
2. [**Obtain hash of an API key**](authentication.md#user-session-hash):
   * Authenticate and obtain a user key via the authentication call.
3.  **Get objects lists with `tracker/list`**:

    * Example API call:

    ```shell
    curl --request POST \
      --url 'https://api.eu.navixy.com/v2/tracker/list' \
      --header 'Content-Type: application/json' \
      --data '{
              "hash": "your_api_key_hash"
            }'
    ```
4.  **Get track lists with `track/list`**:

    * Example API call:

    ```shell
    curl --request POST \
      --url 'https://api.eu.navixy.com/v2/track/list' \
      --header 'Content-Type: application/json' \
      --data '{
              "hash": "your_api_key_hash",
              "tracker_id": 12345
            }'
    ```
5.  **Get the track itself with `track/read`**:

    * Example API call:

    ```shell
    curl --request POST \
      --url 'https://api.eu.navixy.com/v2/track/read' \
      --header 'Content-Type: application/json' \
      --data '{
              "hash": "your_api_key_hash",
              "track_id": 67890
            }'
    ```

## Request conventions

These apply to every operation in this section.

Some of them are shared with the Admin Panel API and are documented in [API conventions](../general/api-conventions.md): the base URLs, the data type names, and the default date and time format.

The rest are **specific to the Platform API**. If you also call the Admin Panel API, do not assume that any of them apply there:

* the account-timezone rule for date and time values, and the [ISO 8601 option](#iso-8601-date-and-time)
* the [string validation rules](#string-validation-rules)
* the [compatibility contract](#ensuring-compatibility)
* acceptance of `GET` as an alternative to `POST`

### API base URL

Platform API operations are served from the regional base URL directly, with no product path in front of them. To make a `user/auth` call on the European platform, use `https://api.eu.navixy.com/v2/user/auth`.

The base URLs are shared with the Admin Panel API and are listed once in [API conventions](../general/api-conventions.md#base-urls).

### API calls format

API calls follow a consistent notation:\
`/resource/sub_resource/action(parameter1, parameter2, [parameter3])`

Parameters can be passed in different ways:

* **HTTP POST `application/json`**: Recommended
* **HTTP POST `application/x-www-form-urlencoded`**: Parameters in the request body
* **HTTP GET**: Not recommended, should be used only for idempotent requests with small parameter size

#### Examples

**HTTP POST `application/json`**

```shell
curl --request POST \
  --url '[api_base_url]/resource/sub_resource/action' \
  --header 'Content-Type: application/json' \
  --data '{"param1": "value1", "hash": "a6aa75587e5c59c32d347da438505fc3"}'
```

**HTTP POST `application/x-www-form-urlencoded`**

```shell
curl --request POST \
  --url '[api_base_url]/resource/sub_resource/action' \
  --data 'param1=value' \
  --data 'hash=a6aa75587e5c59c32d347da438505fc3'
```

**HTTP GET**

```shell
curl '[api_base_url]/resource/sub_resource/action?param1=value1&hash=a6aa75587e5c59c32d347da438505fc3'
```

### Request and response format

To make an API call, send a `POST` request to:`[api_base_url]/resource/action/`

The response will be given with `application/json` content type, even for errors. Response fields and object structure are specific to the API call.

#### Ensuring compatibility

Our API evolves over time, and new methods and JSON object fields are added. We strive to maintain backward compatibility with legacy API clients. However, you **must** ensure that any unsupported JSON object fields by your app are **ignored**, and that your application can handle new JSON fields without breaking. Also, to reduce response size, JSON fields that are NULL are omitted. Your JSON parser should handle missing JSON fields as if they were NULL.

### Authorization and access levels

Unless otherwise noted, every API call requires a valid API Key hash. This hash can be passed in one of the following ways:

1. **As a `hash` parameter of the request body** (root-level property for `application/json`).
2. **As a `hash` parameter of the HTTP query string**.
3.  **As a value of the HTTP header `Authorization`** in the following form:

    ```
    Authorization: NVX SessionHashValue
    ```

### Data types

The type names used throughout this reference, such as `int`, `enum`, `location`, and `locale`, are shared with the Admin Panel API and are defined once in [Data types](../general/api-conventions.md#data-types).

Two of them carry rules specific to the Platform API: `string` values are subject to the [string validation rules](#string-validation-rules), and `date/time` values are resolved in the account's timezone.

### String validation rules

This validation ensures that the provided string adheres to specific content rules. The rules may vary depending on the particular field being validated. The string is checked for the following criteria:

* Empty strings or null values: may be allowed or restricted depending on the specific field.
* Character types:
  * Regular spaces are always allowed (Unicode category "Zs").
  * Other whitespace characters (tabs, line breaks, etc.): may be allowed or restricted depending on the specific field.
  * Control characters (category "Cc" except whitespace characters): not allowed.
  * Private Use characters (category "Co"): not allowed.
  * Surrogate characters (category "Cs"): not allowed.
  * Characters requiring more than 3 bytes in UTF-8 encoding (e.g., some emojis): may be allowed or restricted depending on the specific field.
  * All other characters are allowed.
* Strings consisting entirely of whitespace may be considered invalid for some fields.

### ISO 8601 date and time

By default the Platform API reads and writes a date and time as `YYYY-MM-DD HH:MM:SS`, **in the account's timezone**. That default format is shared with the Admin Panel API and is described in [Date/time formats](../general/api-conventions.md#date-time-formats). The account-timezone rule is specific to the Platform API.

The Platform API can use [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) instead, as `yyyy-MM-dd'T'HH:mm:ssZZ`, which carries an explicit offset and so removes the ambiguity. The Admin Panel API has no equivalent option.

To use ISO 8601 date/time format, pass `true` to:

1. `iso_datetime` parameter of the request body (root-level property for `application/json`).
2. `iso_datetime` parameter of the HTTP query string.
3. HTTP header `NVX-ISO-DateTime`.

#### Example

**JSON request body parameter**

```shell
curl --request POST \
  --url '[api_base_url]/resource/sub_resource/action' \
  --header 'Content-Type: application/json' \
  --data '{"iso_datetime": true, "hash": "a6aa75587e5c59c32d347da438505fc3"}'
```

**Form request parameter**

```shell
curl --request POST \
  --url '[api_base_url]/resource/sub_resource/action' \
  --data 'iso_datetime=true' \
  --data 'hash=a6aa75587e5c59c32d347da438505fc3'
```

**HTTP Header**

```shell
curl --request POST \
  --url '[api_base_url]/resource/sub_resource/action' \
  --header 'Content-Type: application/json' \
  --header 'NVX-ISO-DateTime: true' \
  --data '{"hash": "a6aa75587e5c59c32d347da438505fc3"}'
```

#### Response example with fixed offset date/time

```json
{
  "success": true,
  "user_time": "2014-07-09T07:50:58+05:00",
  "list": [
    {
      "type": "odometer",
      "value": 100500.1,
      "update_time": "2014-03-06T13:57:00+05:00"
    }
  ]
}
```

#### Response example with UTC date/time

```json
{
  "success": true,
  "user_time": "2014-07-09T02:50:58Z",
  "list": [
    {
      "type": "odometer",
      "value": 100500.1,
      "update_time": "2014-03-06T17:57:00Z"
    }
  ]
}
```

## Next steps

* To authenticate and obtain a key, see [Platform authentication](authentication.md).
* To work a whole task through, see [Guides and examples](guides/).
* To look up a specific operation, see [Resources](resources/).
* To look up an error code, see [Errors](../general/errors.md).

For questions and support, contact the [Navixy developer support team](../general/contacts.md).
