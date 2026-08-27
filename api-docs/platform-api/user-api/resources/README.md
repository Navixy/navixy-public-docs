---
title: Resources
description: The operation reference for the Platform API, grouped by resource family.
---

# Resources

Every Platform API operation is documented here, on the page for the resource it acts on. Each page carries the full parameter and response schema for its operations, generated from the specification, plus a panel for sending a test request from the documentation.

Find the family you need below, then use the endpoint table on its page to jump straight to an operation.

## Resource families

| Family | Operations for |
| --- | --- |
| [Tracking](tracking/README.md) | Devices, their tracks and points, sensors, geofences, rules, and working statuses |
| [User commons](commons/README.md) | The account itself, its credentials, sub-users, event history, tags, and platform metadata |
| [Field service](field-service/README.md) | Employees, the tasks and routes assigned to them, and the forms and check-ins they return |
| [Fleet management](fleet/README.md) | Vehicle records, garages, and driver journals |
| [Billing](billing/README.md) | Invoices, payments, transaction history, and service plans |
| [Geo links](location_link.md) | Shareable links that show a tracker's live location without an account |
| [Login redirect](login-redirect.md) | URL parameters that send a user to a chosen section of the login page |
| [Raw IoT Data API](data-warehouse-api/README.md) | Unprocessed device data, on its own base URL and specification |
| [Eco Fleet API](eco-fleet-api/README.md) | Fuel sensor quality, on its own base URL and specification |

For how trackers, tracks, rules, and users relate to each other, see [Key concepts](../getting-started.md#key-concepts).

## Authentication

Almost every operation needs a credential, sent in the `Authorization` header as `NVX <hash>`. See [Platform authentication](../authentication.md) for how to obtain one, and use an API key rather than a session hash for anything long-lived.

Four operations need none, because they are what a caller reaches before having a credential: `user/auth`, `user/resend_activation`, `dealer/get_ui_config`, and `timezone/list`. Each one's reference page says so.

## Base URLs

Most operations live under the `/v2` path of the regional API server. The base URLs are shared with the Admin Panel API and are listed once in [Base URLs](../../general/api-conventions.md#base-urls).

The two smaller APIs do not use `/v2`. Raw IoT Data is served from `/dwh/v1` and Eco Fleet from `/eco_fleet/v1`, on the same regional hosts. Each family page states the base path its operations sit under.

For the data types and the date and time format that apply throughout, see [API conventions](../../general/api-conventions.md). For the parameter transports and the rules specific to this section, see [Request conventions](../getting-started.md#request-conventions).

## OpenAPI specifications

Every operation on every page is generated from an OpenAPI 3.1 specification, so the parameter tables, response schemas, and status codes are the specification rendered rather than prose written by hand. Download the raw JSON to generate a client, load into an API tool, or hand to an AI agent, and treat it as the technical source of truth.

* [Navixy Platform](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/api-docs/platform-api/user-api/reference/Navixy_Platform.json): every operation in this section except the two below.
* [Raw IoT Data](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/api-docs/platform-api/user-api/reference/Raw_IoT_Data.json): the Raw IoT Data API.
* [Eco Fleet](https://raw.githubusercontent.com/Navixy/navixy-public-docs/refs/heads/main/api-docs/platform-api/user-api/reference/Eco_Fleet.json): the Eco Fleet API, whose error model is RFC 7807 problem details rather than the numeric codes everything else uses.

## Support

For questions and support, contact the [Navixy developer support team](../../general/contacts.md).
