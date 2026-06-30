---
title: Green "We'll be back soon" page
description: API server is inoperable or domain configuration mismatch. Check service status or validate domain settings in Admin Panel and Nginx
---

# Green "We'll be back soon" page

## Question

What does the green "We'll be back soon" maintenance page mean?

![](<../.gitbook/assets/Unknown image (57)>)

## Answer

This "green" error is typically a consequence of the `api-server` service being inoperable.

Check:

`systemctl status api-server`

Restart:

`systemctl start api-server`

If the service is working normally, another common reason is incorrect domain configuration. For example, the domain name set in **Admin Panel** does not match what is configured in Nginx. Validate the domain settings to locate the mismatch.
