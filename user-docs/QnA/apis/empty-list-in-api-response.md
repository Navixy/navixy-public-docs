---
description: Handle empty lists gracefully in your client code. Empty responses with success=true indicate no data for your query parameters
---

# Empty list in API response

## Question

Some methods return an empty list/object. Is this expected?

## Answer

Yes.

If `success=true`, the request was processed correctly.

An empty `list: []` means there is simply no data for your query.

Examples:

* no counters configured
* no data in the requested time interval
* no geofences created

This is normal API behavior and can't be changed, as it ensures compatibility with existing integrations.

Tip: handle empty lists gracefully in your client (for example, show "No data available").
