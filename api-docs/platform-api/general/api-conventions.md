---
description: >-
  Conventions shared by the Navixy Platform API and the Navixy Admin Panel API:
  base URLs, data types, date and time format, and request rate limits.
---

# API conventions

The Navixy Platform API and the Navixy Admin Panel API are served from the same hosts and share several conventions. This page is the reference for those: where to send a request, the data types a field can hold, how a date and time is written, and the request rate limits. Rules that apply to only one of the two APIs are documented on that API's own pages.

{% hint style="warning" %}
**Each API has its own credential, and you need one before any call here will work.** Get it from the authentication page for the API you are calling: [Platform authentication](../user-api/authentication.md) or [Admin Panel authentication](../panel-api/authentication.md).

The two are not interchangeable. Both APIs send the credential the same way, in the `Authorization` header, so requests to them look alike. A credential issued by one is still rejected by the other.
{% endhint %}

## Base URLs

All calls use HTTPS. Both APIs are served from the same host, chosen by which Navixy platform your account is on:

| Deployment               | Base URL                        |
| ------------------------ | ------------------------------- |
| Europe                   | `https://api.eu.navixy.com/v2`  |
| North America            | `https://api.us.navixy.com/v2`  |
| Middle East              | `https://api.me.navixy.com/v2`  |
| Self-hosted (on-premise) | `https://api.{your-domain}/v2`  |

The three regional URLs are fixed. On a self-hosted installation, `{your-domain}` stands for the domain your own installation is served from: if that domain is `fleet.example.com`, your base URL is `https://api.fleet.example.com/v2`. Everything after the host is the same as on the regional platforms. If you are not sure which domain to use, ask whoever runs your installation.

The product path follows the base URL:

| API             | Path                  | Example                                          |
| --------------- | --------------------- | ------------------------------------------------ |
| Platform API    | the base URL directly | `https://api.eu.navixy.com/v2/user/auth`         |
| Admin Panel API | `/panel/`             | `https://api.eu.navixy.com/v2/panel/account/auth` |

Two smaller APIs documented alongside the Platform API sit outside `/v2`, on the same regional hosts: Raw IoT Data is served from `/dwh/v1` and Eco Fleet from `/eco_fleet/v1`. Each family page states the base path its own operations use.

## Data types

| Type         | Description                                                                                                                                                                                                                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `bool`       | Boolean: logical type (`true` or `false`).                                                                                                                                                                                                                                                          |
| `byte`       | Signed 8 bits integer in range `[-128 .. 127]`.                                                                                                                                                                                                                                                     |
| `short`      | Signed 16 bits integer in range `[-32,768 .. 32,767]`.                                                                                                                                                                                                                                              |
| `int`        | Signed 32 bits integer in range `[-2,147,483,648 .. 2,147,483,647]`.                                                                                                                                                                                                                                |
| `long`       | Signed 64 bits integer in range `[-9,223,372,036,854,775,808 .. 9,223,372,036,854,775,807]`.                                                                                                                                                                                                        |
| `float`      | Signed 32 bits float number `[3.40282347 x 10^38, 1.40239846 x 10^-45]`.                                                                                                                                                                                                                            |
| `double`     | Signed 64 bits float number `[1.7976931348623157 x 10^308, 4.9406564584124654 x 10^-324]`.                                                                                                                                                                                                          |
| `string`     | String literals. The Platform API additionally applies [string validation rules](../user-api/getting-started.md#string-validation-rules).                                                                                                                                                            |
| `enum`       | String literals from a predefined set.                                                                                                                                                                                                                                                              |
| `date/time`  | String containing date/time in defined formats. See [Date/time formats](#date-time-formats).                                                                                                                                                                                                        |
| `local_time` | String containing local time in `HH:mm:ss` format.                                                                                                                                                                                                                                                  |
| `location`   | JSON object containing geographical coordinates, e.g., `{"lat": 34.178868, "lng": -118.599672}`.                                                                                                                                                                                                    |
| `locale`     | String in format `language[_country]`, where `language` is an [\[ISO 639 alpha-2\]](https://www.loc.gov/standards/iso639-2/php/English_list.php) language code, and `country` is an [ISO 3166 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-2#Current_codes) country code, e.g., `en_US` or `de`. |

## Date/time formats

Both APIs write a date and time as a string in `YYYY-MM-DD HH:MM:SS` format, for example `2026-08-24 09:00:00`.

**The format is the same in both APIs. The timezone it is expressed in is not, so check which API you are calling before you interpret a value.**

**Platform API.** Date and time values are expressed in the account's timezone, both in what you send and in what you get back. Read the account's timezone from `user/get_info` if you need to convert. The Platform API can also use ISO 8601, which carries an explicit offset and removes the question entirely: see [ISO 8601 date and time](../user-api/getting-started.md#iso-8601-date-and-time).

**Admin Panel API.** The timezone is not specified. Treat a returned value as having no stated offset: do not assume UTC, and do not assume the account's timezone. Where you need a definite point in time, take the offset from the account's configured time zone rather than reading it into the value. There is no ISO 8601 option here.

## Request rate limits

The platform limits API requests to 50 requests per second. The limit is applied per user, and also per IP address, which is what constrains an application serving many users from one host. It applies to every Navixy RESTful API, and is counted against the credential a request carries.

**Platform API only:** an API key has its own rate-limit counter, so an integration that exhausts its limit does not block the account's ordinary users. The Admin Panel API has no API keys, and authenticates with a panel session hash only.

Exceeding a limit returns [error 15](errors.md#error-codes), "Too many requests (rate limit exceeded)", with HTTP status `429`.

{% hint style="info" %}
Error 15 covers more than the request rate. Some operations also return it for their own concurrency limits, such as running too many spreadsheet imports at once or generating too many reports in parallel. Read the message it comes with, and check the operation's own error list, to tell which limit you hit: waiting and retrying fixes one, and reducing how much you run at the same time fixes the other.
{% endhint %}

Retry a rate-limited request with exponential backoff rather than immediately.

## See also

* [Errors](errors.md) for the error code table shared by both APIs.
* [Platform authentication](../user-api/authentication.md) and [Navixy platform API](../user-api/getting-started.md) for the Platform API.
* [Admin Panel authentication](../panel-api/authentication.md) and [Navixy Admin Panel API](../panel-api/getting-started.md) for the Admin Panel API.
