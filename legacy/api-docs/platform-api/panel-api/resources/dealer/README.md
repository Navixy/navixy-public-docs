---
description: API call to get information about a telematics service provider (TSP) account.
---

# Dealer

In Navixy, a telematics service provider (TSP) is a company that sells Navixy platform services to its own customers. In the API, a TSP account is called a dealer, so its methods and fields use the word `dealer`. TSPs have access to the Navixy Admin Panel, where they can manage various aspects of the platform, including users, devices, and service plans.

## Dealer object

{% openapi-schemas spec="admin-panel" schemas="Dealer" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

Notes on individual fields:

* `features` lists the allowed [TSP account features](../../../user-api/resources/commons/dealer.md#dealer-features).
* `default_user_time_zone` is a [time zone ID](../timezone.md) applied to users created via [user/upload](../user/README.md#post-panel-user-upload). The same zone is preselected when creating a user in the Navixy Admin Panel.
* `store_period` on the TSP account applies to users on a `demo_tariff`. The `store_period` inside `tariff` is the plan's own maximum data store period. The two are separate values.
* `password_policy` reports the complexity rules enforced for this TSP account, and applies to [dealer/password/update](password.md) as well as to end user passwords.

## API Actions

**API Path**: `panel/dealer/`

***

**Required Permissions:**

* `base: "get_dealer_info"`.

{% openapi-operation spec="admin-panel" path="/panel/dealer/get_info" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* `201` - Not found in the database.
