---
title: Gateways
description: >-
  Information about email gateways objects. Email gateway can be owned by a
  dealer or leased from platform owner.
---

# Gateways

## Email gateway object

A dealer can send notification mail either through a gateway it owns, or through one leased from the platform owner. The two are separate objects, and the list operation returns them separately.

Own gateways currently support the SMTP provider only. Leased gateways are not limited to SMTP: `router` and `mandrill_smtp` have both been observed.

{% openapi-schemas spec="admin-panel" schemas="DealerOwnEmailGateway,LeasableEmailGateway" grouped="true" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-schemas %}

For an SMTP gateway, `params` carries `default_from_address` together with the `mail.smtp.*` transport settings, for example `mail.smtp.host`, `mail.smtp.port`, `mail.smtp.auth`, and the TLS options. The exact set depends on the provider.

## API actions

API path: `panel/gateways/email`.

***

_required permissions_: `email_gateways: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/gateways/email/list" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

A dealer that is itself the platform owner receives an empty `leasable` list.

The originators used when sending through the bound gateway are configured in [notification settings](dealer/settings/notification.md).

#### Errors

[General](../../user-api/backend-api/errors.md#error-codes) types only.
