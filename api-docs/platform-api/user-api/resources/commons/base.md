---
title: Base
description: A health check that does nothing and reports success.
---

# Base

`base/nothing` does nothing and reports success. It is the cheapest call in the API, which makes it useful for two things: checking that the Navixy platform is reachable, and checking that a credential still works.

The second is worth knowing when debugging. A dead API key makes every authenticated call return error 4, which looks the same as a per-operation permission problem. If `base/nothing` fails while an unauthenticated call such as [`timezone/list`](timezone.md#post-timezone-list) still answers, the credential is the problem rather than the endpoint.

## API actions

API base path: `/base`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/base/nothing" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
