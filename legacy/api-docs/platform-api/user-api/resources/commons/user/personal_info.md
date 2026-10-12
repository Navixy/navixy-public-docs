---
title: User personal info
description: The billing identity stored for an account, replaced wholesale on each update.
---

# User personal info

Personal info is the billing identity stored for an account: the legal name, registration numbers, and registered address that invoices are issued against.

{% hint style="warning" %}
This operation **replaces** the stored personal info rather than merging into it. A field omitted from the request is cleared, so send every field you intend to keep.
{% endhint %}

Which fields apply depends on the account's legal type. `iec` and `legal_name` apply to `legal_entity` only. The registered address fields, `okpo_code`, `state_reg_num`, and `tin` apply to `legal_entity` or `sole_trader`.

The operation needs [plugin](../plugin/README.md) 45 to be available to the user.

## API actions

API base path: `/user/personal_info`.

***

{% openapi-operation spec="navixy-platform" path="/user/personal_info/update" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 222 - Plugin not found, when plugin 45 is not available to the user.
