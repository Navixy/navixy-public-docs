---
description: Prefer API keys for User API authentication as they don't expire. Use admin panel session keys for Admin Panel API with caution
---

# User session hash vs admin panel hash vs API key

## Question

User session hash vs admin panel hash vs API key. Which one should I use?

## Answer

To use the Navixy API, you authenticate using either a session key (hash) or an API key.

Example (user API call authenticated by a user session hash):

```
https://api.domain.com/v2/retranslator/list?hash=a6aa75587e5c59c32d347da438505fc3
```

You could also use an API key in place of the user session hash. They look the same and are interchangeable for the User API.

There are three common auth tokens:

1. **User session hash**
   * obtained via `user/auth`
   * expires
   * used for User API
2. **API key**
   * created in UI or via API
   * does not expire
   * used for User API
   * recommended for integrations
3. **Admin panel hash (session key)**
   * obtained via `panel/account/auth`
   * expires
   * used only for Admin Panel API

The easiest way to choose the right token is to look at the URL path:

### User API URL examples

```
https://api.domain.com/v2/retranslator/list
https://api.domain.com/v2/tracker/location/link/read
```

### Admin Panel API URL examples

```
https://api.domain.com/v2/panel/account/auth
https://api.domain.com/v2/panel/tariff/list
```

All Admin Panel resources start with `.../panel/...`.

{% hint style="warning" %}
There are **no API keys** for the Admin Panel API.

So Admin Panel session keys must be handled with extra caution, because obtaining one requires storing panel login/password somewhere.
{% endhint %}

When building integrations, prefer **API keys** for the User API whenever possible.

In summary:

* For the **User API**, prefer **API keys**.
* For the **Admin Panel API**, use **admin panel session keys**, but handle them with caution.

## Links

* [User API authentication](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/authentication)
* [Panel API authentication](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/panel-api/authentication)
