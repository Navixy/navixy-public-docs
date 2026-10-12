---
description: Set up an SMTP server to enable email notifications, invites, and reports in Navixy on-premise. Configure SPF and DKIM records to improve delivery reliability.
---

# Email gateway

SMTP (Simple Mail Transfer Protocol) server is used to send emails, such as notifications, invites, reports, and others. In order to use SMTP with Navixy On-premise, the following requirements should be met:

- The SMTP server can be either remote or local.
- The SMTP server can be with or without authorization (open relay).
- The server must permit to replace the sender in the 'From' header (command mail from:<>).
- It is recommended to add SPF (Sender Policy Framework) record and create DKIM (DomainKeys Identified Mail) for emails. This will protect your emails from being considered as spam by the recipient's server.

Please note that Navixy does not provide an SMTP server and you will need to use your own or a third-party service. It is also important to configure your SMTP settings properly to ensure successful email delivery.