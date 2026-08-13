---
title: Timezone
description: >-
  API call to get information about all supported timezones for the specified
  locale.
---

# Timezone

## API actions

API path: `panel/timezone`.

***

This is the only Admin Panel operation that requires no authentication and no permissions.

{% openapi-operation spec="admin-panel" path="/panel/timezone/list" method="post" %}
[OpenAPI admin-panel](../reference/Admin_Panel.json)
{% endopenapi-operation %}

The `zone_id` value is the identifier used wherever the API accepts a time zone, including `default_user_time_zone` in [service settings](dealer/settings/service.md).

#### Errors

[General](../../user-api/backend-api/errors.md#error-codes) types only.
