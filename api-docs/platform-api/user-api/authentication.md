---
title: Platform authentication
description: How to authenticate to the Platform API, and how to send the credential with a request.
---

# Platform authentication

Almost every Platform API operation needs a credential, and there are two kinds. A **session hash** is what logging in returns, and it expires. An **API key** does not expire and is what an integration should use.

Four operations need none, because a caller reaches them before having a credential: `user/auth`, `user/resend_activation`, `dealer/get_ui_config`, and `timezone/list`.

Start with a session hash to get going, then create an API key and use that for anything that runs unattended.

## User session hash

A session hash is what `user/auth` returns when you send it a login and a password. It is the starting point, and for a person exploring the API it is often all that is needed.

Use one for initial access, for work done on a user's behalf in an interface, and for short-lived scripts.

### How long a session lasts

A session hash expires **30 days after it was issued**.

Two things about this deadline are easy to get wrong:

* **The deadline is absolute, not a period of inactivity.** It is fixed when the session is created, and using the session does not push it back. A client that calls the API every day still loses the session on day 30.
* **30 days is a default, not a constant.** The period is a per-installation setting, so a self-hosted deployment can use a different one. Handle expiry rather than hard-coding the number.

[`user/session/renew`](resources/commons/user/session/README.md) is what moves the deadline. It does not extend the session by a further period from where it stood: it discards the old deadline and sets a fresh full lifetime starting now. Calling it on a session with 2 days left and on one with 29 days left leaves both with the same 30 days.

Because an API key has no expiry at all, it is the credential for anything unattended.

{% hint style="warning" %}
A session hash is not suitable for an unattended integration:

* it expires on a fixed deadline, and a long-running client has to [renew](resources/commons/user/session/README.md) it before that to stay signed in;
* logging out or changing the password invalidates it immediately;
* every integration sharing the account shares the same credential.
{% endhint %}

### Obtaining a session hash

Send the account's login and password to `user/auth`:

```bash
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/user/auth' \
  --header 'Content-Type: application/json' \
  --data '{
    "login": "your@email.com",
    "password": "your_password"
  }'
```

The response carries the hash:

```json
{
  "success": true,
  "hash": "22eac1c27af4be7b9d04da2ce1af111b"
}
```

Save the `hash` value. It authenticates every further request until it expires.

## API keys

An API key works exactly like a session hash except that it never expires, which is what makes it the right credential for an integration. Use one for anything running unattended, and keep the session hash for interactive work.

| | API key | Session hash |
| --- | --- | --- |
| Expiry | Never | 30 days from creation by default, configurable per installation |
| Password change | Unaffected | Invalidated immediately |
| Logout | Unaffected | Invalidated immediately |
| What is stored | The key alone | The login and password |
| Revoking one | Individually | Ends every session together |
| Per integration | One key each | All share one credential |
| Renewal | Not needed | Needed for long sessions |

{% hint style="info" %}
An account may hold up to 20 API keys, and only the account owner can manage them.
{% endhint %}

### Creating an API key

Create keys from the web interface, which is the route intended for a person:

1. Sign in to the Navixy web interface as the account owner.
2. Click your username, then open **API keys**.
3. Click the plus button, give the key a name that identifies what will use it, and save.
4. Copy the key from the table. Use it wherever a hash is expected.

To create and revoke keys through the API instead, see [API keys](resources/commons/api-keys.md). Those operations need a session hash from `user/auth`: an API key cannot manage API keys.

## Sending the credential with a request

A credential can be sent three ways. The reference documents the header form, and every generated example uses it.

### As a request header

Put the credential in the `Authorization` header, after `NVX` and a space. This is the recommended form: it keeps the credential out of the URL and out of the request body, and it works with every method.

{% code title="Format" %}
```http
Authorization: NVX your_hash_or_api_key
```
{% endcode %}

{% code title="Valid" %}
```http
Authorization: NVX 22eac1c27af4be7b9d04da2ce1af111b
```
{% endcode %}

{% code title="Invalid: the space after NVX is missing" %}
```http
Authorization: NVX22eac1c27af4be7b9d04da2ce1af111b
```
{% endcode %}

### In the request body

Send the credential as a root-level `hash` property of the JSON body, or as a `hash` field of a form-encoded body:

```bash
curl --request POST \
  --url 'https://api.eu.navixy.com/v2/tracker/list' \
  --header 'Content-Type: application/json' \
  --data '{"hash": "1dc2b813769d846c2c15030884948117"}'
```

This works with POST only, and it mixes the credential in with the request parameters.

### As a query parameter

Append the credential to the URL as a `hash` parameter:

```
https://api.eu.navixy.com/v2/tracker/list?hash=your_hash_or_api_key
```

{% hint style="danger" %}
Use this for testing only. A credential in a URL is recorded in server logs, browser history, and proxy logs. Never use it in production.
{% endhint %}

{% hint style="info" %}
The same three transports carry the operation's own parameters, not just the credential: a JSON body, a form-encoded body, or query-string values. Each operation's reference documents the JSON body form, because that is the one the specification describes and the one the try-it panel sends. The other two accept the same parameter names.
{% endhint %}

## Recommended flow

1. **Sign in** to the account in the Navixy web interface, as the owner. Only an owner can create API keys.
2. **Create a key per integration**, each named for what uses it, so one can be revoked without affecting the others.
3. **Send the key in the `Authorization` header** on every call, and store it server-side.
4. **Rotate keys periodically**, and revoke any key that is unused or may have been exposed.

## Authentication errors

A failed credential comes back as a normal error response:

```json
{
  "success": false,
  "status": {
    "code": 3,
    "description": "Access denied"
  }
}
```

| Code | Meaning | What to do |
| --- | --- | --- |
| 3 | Wrong hash. The credential is malformed or was revoked. | Check the `NVX ` prefix and the space after it, then re-authenticate. |
| 4 | User or API key not found, or the session ended. | The hash expired or never existed. Obtain a new one. Some operations also return this when given an API key where only a session hash is accepted. |
| 7 | Invalid parameters. | The credential reached the operation but a parameter did not validate. |

Handle 3 and 4 by re-authenticating rather than retrying the same call. For every other code, see [Errors](../general/errors.md).

## Best practices

* Create API keys rather than reusing the account password anywhere.
* Give each integration its own key, so revoking one leaves the others working.
* Name every key for what uses it. The name is the only thing that distinguishes them later.
* Send credentials over HTTPS only. A key travels in clear text.
* Store keys server-side, never in client-side code or a public repository.
* Revoke a key as soon as it is unused or suspected of exposure.
* Handle authentication failures explicitly, including re-authenticating where that is appropriate.
