---
title: Stop device reconfiguration after activation
description: Disable default settings in Service preferences to prevent automatic configuration commands after device activation
---

# Stop device reconfiguration after activation

## Question

I configured my device before connecting it. After activation, settings changed. How do I stop this?

## Answer

By default, the platform can send activation and default configuration commands.

Disable it in Admin Panel:

Settings → Service preferences → Device activation → Don't send default settings

This prevents the platform from sending default settings after activation.

It can also prevent SMS activation commands (if used).

## Links

* [Service preferences](https://www.navixy.com/docs/admin/settings/service-preferences)
