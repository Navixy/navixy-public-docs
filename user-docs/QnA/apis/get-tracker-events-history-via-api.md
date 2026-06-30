---
description: Retrieve tracker event history by calling unread events endpoints or filtering by event type codes and time period. Supports geofence, bracelet removal
---

# Get tracker events history via API

## Question

Can I get full history of tracker events (geofence exit, bracelet removal, etc.) via API?

## Answer

Yes.

Options:

* **Unread events only:** [Get all unread events](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/guides/rules-notifications/work-with-notifications)
* **All events for a tracker over a time period:** [Events for specific trackers and time period](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/guides/rules-notifications/work-with-notifications#events-for-specific-trackers-and-time-period)
* **All events for a user over a time period:** [All user events for a time period](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/guides/rules-notifications/work-with-notifications#all-events-of-a-user-for-a-specific-time-period)

You can filter by event type codes, for example:

`events: ["inzone", "outzone"]`

### How to find event type codes

Call the event types list endpoint:

* [Event types list](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/history/history_type)

Tips:

* Set `locale` to any supported language you need.
* Set `only_tracker_events=true` to list only tracker-related events.

### Implementation tip

You usually fetch event types once during development, then hardcode the event codes you need.

### Real-time notifications

If you want to build something like a live event feed, you can implement simple polling — query the API every few seconds.

## Links

* [Work with notifications](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/guides/rules-notifications/work-with-notifications)
* [Event types list](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/history/history_type)
