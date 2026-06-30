---
description: Use WebSockets for real-time GPS data streaming without polling overhead. Use get_states only if WebSockets unavailable in your environment
---

# WebSockets vs get\_states API for real-time tracking data

## Question

WebSockets vs `tracker/get_states`. Which should I use for real-time tracking data?

## Answer

Developing third‑party software may require reading real-time tracking data. There are two main methods you can use:

* WebSockets
* `tracker/get_states` API method

In most cases, **WebSockets** is what you want to implement. WebSockets natively support real‑time connections for reading GPS-related device data.

The `get_states` method is more for getting the last known valid GPS data. However, if you can't use WebSockets in your application for some reason, you can set up polling for GPS data using `get_states`.

### WebSockets

Pros:

* Natively supports real‑time connections
* A fast and reliable way of getting real‑time tracking data
* Doesn't load the API in terms of requests per second (unlike polling)

Cons:

* May be harder to implement compared to a simple HTTP request API usage

### get\_states

Pros:

* Simple HTTP request

Cons:

* Doesn't support real‑time updates (requires polling)
* In a real‑time scenario, each API call loads the platform in terms of allowed requests per second

## Links

* [WebSocket](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/websocket)
* [`tracker/get_states`](https://www.navixy.com/docs/navixy-api/user-api/backend-api/resources/tracking/tracker#get_states)
