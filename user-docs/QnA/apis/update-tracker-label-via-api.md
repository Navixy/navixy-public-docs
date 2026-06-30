---
description: Call tracker/settings/update with correct group_id to update the label. Retrieve current group_id via tracker/list to avoid moving tracker
---

# Update tracker label via API

## Question

Is there an API endpoint to update a tracker label/name?

## Answer

Yes.

Use `tracker/settings/update`.

Be careful: this call also requires `group_id`.

If you send the wrong `group_id`, the tracker can be moved.

Safe bulk approach:

1. Call `tracker/list` to get `tracker_id` and current `group_id`.
2. Call `tracker/settings/update` with the same `group_id` and new `label`.

## Links

* [`tracker/list`](https://www.navixy.com/docs/navixy-api/user-api/backend-api/resources/tracking/tracker#list)
* [`tracker/settings/update`](https://www.navixy.com/docs/navixy-api/user-api/backend-api/resources/tracking/tracker/settings/index#update)
