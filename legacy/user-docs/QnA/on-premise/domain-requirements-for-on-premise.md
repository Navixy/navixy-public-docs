---
title: Domain requirements for On-premise
description: "Register an A-type DNS record pointing to your server to enable SSL certificates and HTTPS. Optional: separate domains for API and admin panel"
---

# Domain requirements for On-premise

## Do I need to use a domain for my instance?

It is not mandatory, but it is highly recommended. Only domains can issue SSL certificates and therefore provide a secure HTTPS connection. If you configure to use only an IP address, you only have an unsecured HTTP connection.
This is why configuring a web server to use an IP address is recommended only on classified subnets that are not accessible from the outside.

## What are requirements for the domain?

The only requirement is that it must be an "A" type DNS record pointing to your server.

## Do I need to register dedicated domains for API and admin panel?

This is optional and not a requirement. You can do this for your convenience. In the absence of dedicated domains, access to API and admin panel can be provided via subdirectories of the base domain of your instance, for example:

* domain.com/api/
* domain.com/panel/
