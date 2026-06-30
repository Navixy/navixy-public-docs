---
title: Use a custom domain for the platform
description: "Configure a CNAME subdomain record pointing to saas.navixy.com, then enable it in Service preferences in Admin Panel"
---

# Use a custom domain for the platform

## Question

How can I customize my web address for the platform?

### Answer

1. Create a CNAME record pointing to:
   * `saas.navixy.com` (EU instance)
   * `saas.us.navixy.com` (US instance)
   * `saas.me.navixy.com` (Middle East instance)
2. The CNAME must be a subdomain (for example `platform.yourdomain.com`).
   * It cannot be a parent domain like `yourdomain.com`.
3. In Admin Panel, go to Settings → Service preferences → Domain name.
4. For HTTPS, open a support ticket to enable SSL.

### Links

* [Domain name](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/settings/domain-name)
