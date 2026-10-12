---
title: Feedback
description: Send a message from the user to platform support by email.
---

# Feedback

This operation sends a message from the user to platform support, which is how an in-application "ask for help" or "suggest a feature" control reaches a human. A screenshot and a browser log can travel with it, and arrive as email attachments.

## Feedback object

```json
{
  "text": "My feedback",
  "useragent": "Chrome/87.0.4280.88",
  "platform": "Windows NT 10.0; Win64; x64",
  "screenshots": ["encoded image1", "encoded image2"],
  "log": <log_file>
}
```

* `text` - string. The feedback text. Cannot be null.
* `useragent` - optional string. The user's browser.
* `platform` - optional string. The user's operating system.
* `screenshots` - optional string array. Base64-encoded data URL images, for example `data:image/jpeg;base64,[encoded image]`.
* `log` - optional log file holding the browser log.

## API actions

API base path: `/feedback`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

{% openapi-operation spec="navixy-platform" path="/feedback/send_email" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
