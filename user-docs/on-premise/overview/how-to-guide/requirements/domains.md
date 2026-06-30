---
description: Configure domain names and DNS A-records for Navixy on-premise. Set up records for the API, admin panel, and monitoring service to enable platform access.
---

# Domains

Navixy is a web-based platform that is usually deployed in a public network. Therefore, a domain name is typically required to access the platform. While using an IP address is possible, domain names significantly improve the presentation and ease of use of the service.

To use a domain name, an account with a public DNS hosting service is necessary. Many DNS hosting services are available, with one of the most popular being GoDaddy.

Typically, a DNS hosting service provides an admin panel to create and control domain names. For Navixy, several A-type DNS records are needed, pointing to the Navixy server.

Suppose the domain name is "[your-domain.com](http://your-domain.com)"; here's an example of the domain configuration:

- [api.your-domain.com](http://api.your-domain.com): domain used for API calls and backend operations
- [panel.your-domain.com](http://panel.your-domain.com): domain used for accessing the admin panel. It can also be [admin.your-domain.com](http://admin.your-domain.com) or anything else.
- [my.your-domain.com](http://my.your-domain.com): domain used for accessing the monitoring service. This domain will be used by users. It can also be [gps.your-domain.com](http://gps.your-domain.com), [pro.your-domain.com](http://pro.your-domain.com), or anything else.

After creating the A-type records, they can be used for the Navixy On-premise installation.