---
title: Navixy Developer Documentation
description: >-
  The Navixy Platform API and Admin Panel API: what each one is for, how to tell
  which you need, and where to start.
---

# Navixy Developer Documentation

[Navixy](https://navixy.com), developed by [SquareGPS](https://squaregps.com), is a GPS and vehicle telematics platform. This documentation covers the Platform API and Admin Panel API: everything you need to query, control, and build on top of Navixy programmatically.

They are two separate APIs with separate credentials, and each has its own getting started guide. Pick the one that matches what you are building.

<table data-view="cards" data-card-size="large">
  <thead>
    <tr>
      <th></th>
      <th></th>
      <th data-hidden data-card-target data-type="content-ref"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>🛰 Platform API</strong></td>
      <td>Build new applications or automate any workflow available to a Navixy platform user. The API exposes the complete feature set of the web interface.</td>
      <td><a href="user-api/getting-started.md">user-api/getting-started.md</a></td>
    </tr>
    <tr>
      <td><strong>⚙️ Admin Panel API</strong></td>
      <td>Manage a reseller deployment: user accounts, devices, tariffs, billing, and dealer-level operations.</td>
      <td><a href="panel-api/getting-started.md">panel-api/getting-started.md</a></td>
    </tr>
  </tbody>
</table>

## Which API do you need?

Both APIs are JSON over HTTPS, and both are documented from an OpenAPI specification. The dividing line is whose data you are acting on. The Platform API acts **as a platform user, on that user's own account**. The Admin Panel API acts **as a dealer, on the accounts the dealer manages**.

| Dimension | Platform API | Admin Panel API |
| --- | --- | --- |
| You are | a platform user, or an application acting for one | a dealer or reseller administering a deployment |
| It covers | the signed-in user's own account, sessions and settings, tracking (trackers, tracks, geofences, sensors, rules), fleet vehicles and maintenance, field service (tasks, places, employees), reports and tags, billing, and sub-users | dealer accounts, the end users beneath them, device registration and plans, activation codes, tariffs, sub-dealers, and panel branding and notification settings |
| You authenticate with | a user session hash, or an API key | a panel session hash |
| Reference | [Resources](user-api/resources/README.md) | [Admin Panel resources](panel-api/resources/README.md) |
| Start here | [Navixy platform API](user-api/getting-started.md) | [Navixy Admin Panel API](panel-api/getting-started.md) |

{% hint style="warning" %}
**The two sets of credentials are not interchangeable.** Both APIs send a credential in the `Authorization` header, and both name the scheme the same way. A hash issued by one is still rejected by the other. They also differ in what the header may carry, in whether a session can be renewed, and in how permissions work. Each API's authentication page is the only source for its own API: [Platform authentication](user-api/authentication.md) and [Admin Panel authentication](panel-api/authentication.md).
{% endhint %}

## What both APIs share

| Shared behaviour | Where it is documented |
| --- | --- |
| Base URLs, data types, the date and time format, and request rate limits | [API conventions](general/api-conventions.md) |
| The error response envelope and the error code table | [Errors](general/errors.md) |
| Parameters in a JSON request body, also accepted form-encoded or as a query string | [Request conventions](user-api/getting-started.md#request-conventions) for the Platform API, [Using authentication in API requests](panel-api/authentication.md#using-authentication-in-api-requests) for the Admin Panel |

Everything else is documented per API, including authentication, and should not be assumed to carry across.

## More APIs in the Navixy ecosystem

The Platform API and Admin Panel API cover the core of the Navixy platform. For specialized use cases, Navixy provides two additional API surfaces:

| API | What it covers |
| --- | --- |
| [IoT Logic API](https://navixy.com/docs/iot-logic-api/) | Configure and manage IoT Logic flows, write DSA scripts, use the Navixy expression language, and work directly with device-level behavior and protocols |
| [Repository API](https://navixy.com/docs/navixy-repository-api/) | GraphQL interface for managing geo-objects, assets, schedules, and device configuration at scale |

## Troubleshooting

* **Error codes:** all error codes and their meanings are documented in [Errors](general/errors.md).
* **Session expired:** re-authenticate to get a new hash. How long a session lasts, and whether it can be renewed, differs between the two APIs: see [Platform authentication](user-api/authentication.md) and [Admin Panel authentication](panel-api/authentication.md).
* **Too many requests:** see [Request rate limits](general/api-conventions.md#request-rate-limits).
* **Need help?** Contact the Navixy developer support team via [Contact Us](general/contacts.md).

{% hint style="info" %}
To explore and test API calls interactively, use the [Navixy API Sandbox Postman collection](general/api-tools/postman.md).
{% endhint %}
