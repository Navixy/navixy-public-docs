---
title: Getting Started
description: Getting started with Navixy API
---

# Navixy Developer Documentation

[Navixy](https://navixy.com), developed by [SquareGPS](https://squaregps.com), is a GPS and vehicle telematics platform. This documentation covers the Platform API and Admin Panel API: everything you need to query, control, and build on top of Navixy programmatically.

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

## Base URLs

All API calls use HTTPS. Choose the base URL that matches your platform deployment:

| Region | Base URL |
| --- | --- |
| Europe | `https://api.eu.navixy.com/v2` |
| North America | `https://api.us.navixy.com/v2` |
| Middle East | `https://api.me.navixy.com/v2` |
| On-Premise | `https://api.your_domain/v2` |

Platform API calls use the base URL directly. Admin Panel API calls append `/panel/`. For example: `https://api.eu.navixy.com/v2/panel/account/auth`.

## Getting started

{% stepper %}
{% step %}
### Identify your use case

Decide which API fits your task:

* **Platform API:** you are building an application or integration that works with tracking data, devices, fleet, field service, or reports on behalf of a platform user.
* **Admin Panel API:** you are managing a Navixy reseller deployment — provisioning users or devices, handling tariffs, or automating dealer-level account operations.

{% hint style="warning" %}
The two APIs use separate authentication and separate base paths. A session hash from one cannot be used in the other.
{% endhint %}
{% endstep %}

{% step %}
### Authenticate

{% tabs %}
{% tab title="Platform API" %}
Authenticate as a platform user to obtain a session hash or API key.

→ [Platform authentication](user-api/authentication.md)
{% endtab %}

{% tab title="Admin Panel API" %}
Authenticate as a dealer or technical account to obtain a panel session hash.

→ [Admin Panel authentication](panel-api/authentication.md)
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### Make a test call

Use the hash from the previous step to confirm your setup is working.

{% tabs %}
{% tab title="Platform API" %}
{% code overflow="wrap" %}
```sh
curl -X POST 'https://api.eu.navixy.com/v2/tracker/list' \
  -H 'Content-Type: application/json' \
  -d '{"hash": "22eac1c27af4be7b9d04da2ce1af111b"}'
```
{% endcode %}

A successful response returns a JSON array of GPS trackers on the account.
{% endtab %}

{% tab title="Admin Panel API" %}
{% code overflow="wrap" %}
```sh
curl -X POST 'https://api.eu.navixy.com/v2/panel/user/list' \
  -H 'Content-Type: application/json' \
  -d '{"hash": "1dc2b813769d846c2c15030884948117", "limit": 10}'
```
{% endcode %}

A successful response returns a JSON array of platform users managed by your dealer account.
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### Explore the reference

{% tabs %}
{% tab title="Platform API" %}
→ [Backend functionality](user-api/backend-api/README.md)

Each resource section follows a consistent structure: object definition, available actions, parameters, request examples, response, and error codes.
{% endtab %}

{% tab title="Admin Panel API" %}
→ [Admin Panel resources](panel-api/resources/README.md)

The Panel API mirrors the structure of the Platform API reference, with the same conventions for parameters, responses, and error codes.
{% endtab %}
{% endtabs %}
{% endstep %}
{% endstepper %}

{% hint style="info" %}
To explore and test API calls interactively, use the [Navixy API Sandbox Postman collection](general/api-tools/postman.md).
{% endhint %}

## More APIs in the Navixy ecosystem

The Platform API and Admin Panel API cover the core of the Navixy platform. For specialized use cases, Navixy provides two additional API surfaces:

| API | What it covers |
| --- | --- |
| [IoT Logic API](https://navixy.com/docs/iot-logic-api/) | Configure and manage IoT Logic flows, write DSA scripts, use the Navixy expression language, and work directly with device-level behavior and protocols |
| [Repository API](https://navixy.com/docs/navixy-repository-api/) | GraphQL interface for managing geo-objects, assets, schedules, and device configuration at scale |

## Troubleshooting

* **Error codes:** all error codes and their meanings are documented in [Errors](user-api/backend-api/errors.md).
* **Session expired:** session hashes expire after 24 hours. Re-authenticate with `user/auth` (Platform API) or `panel/account/auth` (Admin Panel API) to get a new hash.
* **Need help?** Contact the Navixy developer support team via [Contact Us](general/contacts.md).

## API limits

To ensure system stability for all customers, the platform limits API requests to 50 requests per second per user and per IP address (for applications serving multiple users). These limits are applied based on user session hash and API keys.
