---
title: Getting started
description: What the Admin Panel API manages, its core concepts, and how to use this documentation.
---

# Navixy Admin Panel API

The Admin Panel API (or Panel API for short) lets developers manage the Navixy platform at the dealer or reseller level: end-user accounts, devices, plans, and dealer-wide settings. Use it to build integrations and internal tools that automate administrative work instead of using the Admin Panel interface by hand.

The structure of the Admin Panel API mirrors that of the [Backend API](../user-api/backend-api/), so it helps to read that page first. The main differences are the authorization scheme and the request paths. Requests and responses use JSON over HTTPS.

Every operation is documented from a single OpenAPI specification, so each resource page under [Resources](resources/) carries the full parameter and response schema for its operations, along with a panel for sending a test request. Use those pages as the reference; this page orients you to the API as a whole.

Use the API to:

* Provision and manage end-user accounts and their balances.
* Register and clone devices, and move them between users.
* Define and price service plans.
* Issue activation codes so users can register devices themselves.
* Brand the platform and configure service and notification settings.
* Run sub-dealers that resell independently.

The API is built for developers and system integrators who manage a Navixy reseller deployment: automating dealer-level operations, building custom admin tooling, or provisioning users and devices at a scale the Admin Panel interface doesn't handle well by hand.

## How to use this documentation

This section documents the Admin Panel API in full. Choose the path below depending on who, or what, is reading.

### For developers

Start with [Key concepts](#key-concepts) below to see how dealers, users, trackers, and plans fit together, then get access: [Admin Panel authentication](authentication.md) covers obtaining a session hash and, for shared or automated access, a technical service account.

Once you are authenticated, look up the operation you need in [Resources](resources/). Each resource family page carries the full parameter and response schema, plus a panel for sending a test request. [Errors](../user-api/backend-api/errors.md) covers the error code table shared with the Platform API.

### For AI agents and LLMs

* **[OpenAPI specification](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/api-docs/platform-api/panel-api/reference/Admin_Panel.json)**: the raw JSON specification for every endpoint, parameter, and schema. Every resource page renders it directly, so the parameter tables, response schemas, and status codes you see are generated from it rather than written by hand. Treat it as the technical source of truth.
* **[Navixy docs via MCP](https://app.gitbook.com/s/gh5cGQ23uFYTcp7Fj7Yd/using-navixy-documentation-with-ai)**: connect to query this documentation interactively instead of parsing static pages.

## Key concepts

A dealer owns users directly, or indirectly through sub-dealers that resell on the dealer's behalf. Each user owns trackers, the devices registered to their account. Tariffs price a tracker's service, and orders record the equipment the dealer has purchased to supply those devices.

| Term | What it is |
| --- | --- |
| Dealer | A reseller or distributor of the Navixy platform, with access to the Admin Panel to manage its users, devices, and service plans. |
| Sub-dealer (Sub PaaS) | A dealer operating underneath another dealer, called Sub PaaS in the interface and `subpaas` in API paths, with its own users, devices, and settings. |
| User | An end-user account that belongs to a dealer, either an organization or an individual, and can have sub-users that grant access to multiple employees. |
| Tracker | A device as presented to one owning user. |
| Clone | A second tracker sharing the same underlying device, so two users can see the same device independently. |
| Source | The underlying device behind a tracker and its clones. Several operations act on the source instead of on the tracker. |
| Order | An equipment order placed by the dealer. |
| Tariff | A service plan a dealer offers to its users, called a plan in the interface and returned as a `Plan` object in the API. |
| Tariff defaults | A dealer-level setting that determines which tariff a newly registered device lands on and what bonus and free period it receives. It is keyed by device type and separate from any individual tariff. |
| Activation code | A code tied to a specific tariff that lets a user register a device with the tariff, balance bonus, and free period already configured. |
| Session | A hash returned by authenticating, valid for 24 hours. Admin Panel sessions are separate from Platform API sessions, so a hash from one can't be used with the other. |
| Technical service account | A reduced-privilege account created by Navixy support, used instead of personal credentials for shared or automated access. |

See [Admin Panel authentication](authentication.md) for how to obtain and use a session hash, and [Technical service accounts](authentication.md#technical-service-accounts) for the full comparison with full admin accounts.

## Admin Panel API permissions

A permission is a pair of a category, for example `trackers`, and an operation, for example `read`. The API compares this pair against what the requested operation requires, and returns an "Operation not permitted" error on a mismatch.

Most Admin Panel API calls require a permission, but not all of them do: `panel/account/auth` and `panel/timezone/list` need no authentication and no permissions, and a few operations, such as `account/get_permissions` and `account/logout`, need only a valid session.

See [Admin Panel permissions](authentication.md#admin-panel-permissions) for the full list of categories and operations, and the [`get_permissions`](resources/account.md#post-panel-account-get_permissions) operation to check what a live session is allowed to do.

## Next steps

* To authenticate and get a session hash, see [Admin Panel authentication](authentication.md).
* To look up a specific operation's parameters and response, see [Resources](resources/).
* To look up an error code, see [Errors](../user-api/backend-api/errors.md).

For questions and support, contact the [Navixy developer support team](../general/contacts.md).
