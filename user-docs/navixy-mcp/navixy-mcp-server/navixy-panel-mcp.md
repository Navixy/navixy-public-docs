# Navixy Panel MCP (navixy-panel)

Navixy Panel MCP connects your AI client to the Navixy Admin Panel. Use it to manage users, monitor devices across your dealer account, check billing transactions, and review tariff plans right in your AI client.

This page describes what Navixy Panel MCP can do and provides a full reference for all available tools (operations your AI client can perform), including parameter names and example prompts for developers building integrations. If you haven't set up the connection yet, refer to [Getting started](https://claude.ai/mcp-test/navixy-mcp-server/getting-started.md).

{% hint style="info" %}
Navixy Panel MCP is intended for telematics providers who manage a Navixy dealer account. If you are an end user, see [Navixy User MCP](https://claude.ai/mcp-test/navixy-mcp-server/navixy-user-mcp.md) instead.
{% endhint %}

**Endpoint:** `https://platform.mcp.navixy.com/panel/mcp`

**See also:**

* [Navixy User MCP](https://claude.ai/mcp-test/navixy-mcp-server/navixy-user-mcp.md)
* [Troubleshooting](../troubleshooting.md)

## What you can do with Navixy Admin MCP

### Check your dealer account

Your AI client can retrieve your dealer account details, including your current tariff plan, active tracker count, available features, balance, and platform domain.

Example prompts:

```
What is my current dealer tariff plan?
```

```
How many active trackers do I have in my dealer account?
```

### Manage users

Your AI client can list all users in your dealer account with filtering, sorting, and pagination. It can retrieve detailed information about a specific user, including their balance, contact details, tracker count, and account status. It can also export user data as CSV for use in external tools.

Example prompts:

```
List all users in my dealer account.
```

```
Show me the details for user ID 333825.
```

```
Export my user list with email, balance, and phone number columns.
```

### Review billing

Your AI client can retrieve billing transactions for a specific user within a date range. Queries are limited to a maximum time span of one month per request.

Example prompts:

```
Show me all transactions for user ID 333825 in April 2026.
```

```
What billing activity has there been on user ID 333825 this month?
```

### Monitor devices

Your AI client can list all trackers across your dealer account, with optional filtering by user, tariff plan, or a text search matching device label, model, IMEI, or phone number. It can retrieve detailed information about a specific tracker, including its device model, tariff plan, connection status, assigned user, and last connection time.

Example prompts:

```
List all trackers in my dealer account.
```

```
Show me all trackers assigned to user ID 333825.
```

```
Show me the details for tracker ID 2988062.
```

### Check tracker activation history

Your AI client can retrieve a month-by-month breakdown of active (billed) trackers across your dealer account for any period, including tracker labels, IDs, and assigned user IDs for each month. This is useful for monitoring platform growth or reconciling billing periods.

Example prompts:

```
Show me how many trackers were active each month in the first quarter of 2026.
```

```
What was the active tracker count for March 2026?
```

### Work with tariff plans

Your AI client can list tariff plans available in your dealer account and retrieve the full details of a specific plan, including pricing, billing algorithm, storage period, and included features. Note that tariff plans belonging to a parent dealer are not visible.

Example prompts:

```
List all available tariff plans.
```

```
Show me the details of tariff plan ID 19917.
```

### Work with tracker bundles and equipment

{% hint style="info" %}
Tracker bundle and equipment tools require tracker bundle management permissions in your dealer account. Contact your Navixy account manager if these tools return a permissions error.
{% endhint %}

Your AI client can list all tracker bundles in your account and retrieve the details of a specific bundle by IMEI. It can also list all available GPS device models and equipment types supported for bundle creation.

Example prompts:

```
List all tracker bundles in my account.
```

```
What equipment models are available for new bundles?
```

## Tools reference

### Dealer

| Tool                | What it does                                                                                                  | Parameters | Example prompt                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------- | ---------- | ---------------------------------------- |
| `panel_dealer_info` | Returns current dealer account details: tariff, active tracker count, features, balance, and platform domain. | None       | "What is my current dealer tariff plan?" |

### Users

| Tool                     | What it does                                                                                                      | Parameters                                                                                                                                                                                                                  | Example prompt                                            |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| `panel_user_list`        | Lists users with optional filtering, sorting, and pagination.                                                     | `filter` (optional), `order_by` (optional: `id`, `login`, `last_name`, `balance`, `bonus`, `phone`, `post_city`), `ascending` (optional, default true), `limit` (optional), `offset` (optional), `hide_inactive` (optional) | "List all users in my dealer account."                    |
| `panel_user_read`        | Returns detailed information about a specific user: balance, contact details, tracker count, account status.      | `user_id` (required)                                                                                                                                                                                                        | "Show me the details for user ID 333825."                 |
| `panel_user_export`      | Exports user list as CSV with configurable columns.                                                               | `columns` (optional, e.g. `["id", "login", "balance", "phone"]`), `filter` (optional), `order_by` (optional), `ascending` (optional), `limit` (optional), `offset` (optional), `hide_inactive` (optional)                   | "Export my user list with email and balance columns."     |
| `panel_transaction_list` | Returns billing transactions for a specific user within a date range. Maximum time span of one month per request. | `user_id` (required), `from` (required, `YYYY-MM-DD HH:mm:ss`), `to` (required, `YYYY-MM-DD HH:mm:ss`, maximum one-month range), `limit` (optional)                                                                         | "Show me all transactions for user 333825 in April 2026." |

### Trackers

| Tool                           | What it does                                                                                                                    | Parameters                                                                                                                                                                                                                                                                                        | Example prompt                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| `panel_tracker_list`           | Lists all trackers in the dealer account with optional filtering and pagination.                                                | `user_id` (optional), `tariff_id` (optional), `filter` (optional, matches label, IMEI, model, phone, user ID), `order_by` (optional: `id`, `label`, `status`, `model`, `device_id`, `phone`, `creation_date`, `last_connection`), `ascending` (optional), `limit` (optional), `offset` (optional) | "List all trackers assigned to user ID 333825."        |
| `panel_tracker_read`           | Returns detailed tracker information: device model, IMEI, tariff, connection status, assigned user, last connection time.       | `tracker_id` (required)                                                                                                                                                                                                                                                                           | "Show me the details for tracker ID 2988062."          |
| `panel_tracker_active_history` | Returns month-by-month active (billed) tracker counts for a given period, including tracker labels, IDs, and assigned user IDs. | `from` (required, `YYYY-MM`), `to` (required, `YYYY-MM`)                                                                                                                                                                                                                                          | "How many trackers were active each month in Q1 2026?" |

### Tariff plans

| Tool                | What it does                                                                                                                                                          | Parameters             | Example prompt                              |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- | ------------------------------------------- |
| `panel_tariff_list` | Lists tariff plans available in the dealer account. Optionally filters by ID, name, price, or device type. Tariff plans belonging to a parent dealer are not visible. | `filter` (optional)    | "List all available tariff plans."          |
| `panel_tariff_read` | Returns full details of a specific tariff plan: pricing, billing algorithm, storage period, included features.                                                        | `tariff_id` (required) | "Show me the details of tariff plan 19917." |

### Tracker bundles and equipment

| Tool                        | What it does                                                                                                                                          | Parameters          | Example prompt                                                  |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------------------- |
| `panel_tracker_bundle_list` | Lists all tracker bundles with optional filtering by ID, IMEI, model code, ICCID, or assignment time. Requires tracker bundle management permissions. | `filter` (optional) | "List all tracker bundles in my account."                       |
| `panel_tracker_bundle_read` | Returns detailed information about a specific tracker bundle. Requires tracker bundle management permissions.                                         | `imei` (required)   | "Show me the details for the bundle with IMEI 359632100000001." |
| `panel_equipment_list`      | Lists all available GPS device models and equipment types. Requires tracker bundle management permissions.                                            | None                | "What GPS device models are available?"                         |

### Reference

| Tool                  | What it does                                      | Parameters                           | Example prompt                             |
| --------------------- | ------------------------------------------------- | ------------------------------------ | ------------------------------------------ |
| `panel_timezone_list` | Lists all supported timezones for a given locale. | `locale` (required, e.g. `en`, `ru`) | "List all supported timezones in English." |
