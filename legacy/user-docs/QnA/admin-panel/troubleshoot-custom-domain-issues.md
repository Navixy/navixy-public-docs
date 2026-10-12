---
title: Troubleshoot custom domain issues
description: "Verify DNS records point to saas.navixy.com with CNAME type; use MXToolbox or dig to validate your domain configuration"
---

# Troubleshoot custom domain issues

## Question

How to troubleshoot custom domain issues?

## Answer

Before troubleshooting, review the domain name guide:

* [Domain name guide](https://www.navixy.com/docs/admin/settings/domain-name)

Then verify DNS:

1. Domain points to `saas.navixy.com` (or `saas.us.navixy.com` for US).
2. The record type is **CNAME**, not A.

You can validate using MXToolbox (or `dig` if you're on Linux/WSL).

## Links

* [Domain name guide](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/settings/domain-name)
* [MXToolbox](https://mxtoolbox.com/)
