---
title: Use your own email gateway for notifications
description: Configure SMTP with DKIM and DMARC DNS records in Admin Panel to route platform emails through your own server
---

# Use your own email gateway for notifications

## Question

Can I use my own email system for outgoing emails?

## Answer

Yes, but it requires SMTP support plus DNS records.

### DKIM

Create two CNAME records:

1. Name: `mte1._domainkey.yourdomain.com` → Value: `dkim1.mandrillapp.com`
2. Name: `mte2._domainkey.yourdomain.com` → Value: `dkim2.mandrillapp.com`

Replace `yourdomain.com` with your domain.

### DMARC

Create a TXT record:

* Host: `_dmarc.yourdomain.com`
* Value: `v=DMARC1; p=none`

### TXT verification

Create a TXT record with the value:

`mandrill_verify.PASTECODE`

`PASTECODE` is provided by Support on request.

### Configure in Admin Panel

In Admin Panel → Account Management → Email Gateways, configure:

* encryption type
* whether to trust all hosts
* SMTP host and port
* username/password
* sender email

## Links

* [Email gateway](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/settings/messaging-gateways/email-gateway)
