---
title: Order
description: API call to read the order by its ID.
---

# Order

## Order object

{% openapi-schemas spec="admin-panel" schemas="Order" grouped="true" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-schemas %}

Fields with no value are omitted from the response rather than returned as `null`.

## API actions

API path: `panel/order`.

***

_required permissions_: `tracker_bundles: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/order/read" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 – Not found in the database - if the specified order does not exist, or belongs to another dealer.
