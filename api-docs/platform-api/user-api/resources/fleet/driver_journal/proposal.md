---
title: Trip proposals
description: The raw trips over a period that a driver journal entry can be created from.
---

# Trip proposals

A proposal is a trip the Navixy platform detected over a period that has not yet been categorised. Proposals are the raw material for the [driver journal](entry.md): read them for a period, decide what each trip was, and create entries from them.

For a walkthrough, see [how to use driver journals](../../../guides/fleet-management/driver-journals.md).

## API actions

API base path: `/driver/journal/proposal`.

Proposal objects are created by dividing a track at each driver change. If there was no driver change on the track, then the track is returned entirely. Tracks are selected by intersecting their date range with the date range in the request.

This call needs the `driver_journal` feature on the tracker's plan.

***

{% openapi-operation spec="navixy-platform" path="/driver/journal/proposal/list" method="post" %}
[OpenAPI navixy-platform](../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../general/errors.md#error-codes):

* 236 - The tracker's plan does not include the driver journal feature.
