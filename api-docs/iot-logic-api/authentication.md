---
description: IoT Logic uses your Navixy Platform credential. Which credential type to use, and how to send it with a request.
tags:
  - auth
---

# Authentication

IoT Logic runs on the Navixy platform, so it authenticates with the same credential as the rest of the Platform API: a user session hash or an API key. If you already have one, there is nothing IoT Logic specific to set up. Reuse it here.

## Which credential to use

Use an API key for IoT Logic work. Flows run unattended, and an API key does not expire, unlike a session hash. See [Platform authentication](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/authentication) for how the two credential types differ, how to obtain a session hash, and how to create an API key from the web interface.

To list, create, or delete API keys through the API itself, see [API keys](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/resources/commons/api-keys). Those operations need a standard session hash: an API key cannot manage API keys.

## Sending the credential

Send the credential in the `Authorization` header, after `NVX` and a space:

```bash
curl --request GET \
  --url 'https://api.eu.navixy.com/v2/iot/logic/flow/list' \
  --header 'Authorization: NVX your_hash_or_api_key'
```

A credential can also be sent in the request body or as a query parameter. See [Platform authentication](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/authentication) for both forms, and when each one is appropriate.

## Authentication errors

Authentication errors use the same codes across the platform, including IoT Logic. See [Errors](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/general/errors).
