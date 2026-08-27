---
title: Login redirect
description: GET parameters that send a user straight to a chosen section of the login page, in a chosen language.
---

# Login redirect

A login redirect is a set of GET parameters on your login page URL. Link to it from your own website and a user lands on the section you chose, in the language you chose, instead of the default login form.

{% hint style="info" %}
This is the one thing in this section that is not an API call. Nothing is requested and nothing is returned: you build a URL, and the Navixy platform reads it when the user arrives.
{% endhint %}

### Page section

You can define the section which your users land by default with `partition` parameter:

* `user`– user login page (used by default)
* `demo` – access a chosen demo account
* `register_fast` – quick registration form
* `register_full` – full registration form
* `password_remind` – password reminder

### Language

Use `locale` parameter to define which language will be used:

* `en_EN`– English
* `es_ES` – Spanish
* `ru_RU` – Russian
* etc.

If this parameter is omitted, the language which was set by default for your service will be used.

### Examples

The next code will land user on login section with Spanish language:

```
http://<your_login_page_url>/login/?partition=demo&locale=es_ES
```

The code below lands user on quick registration form with default language set by default:

```
http://<your_login_page_url>/login/?partition=quick_register&locale=es_ES
```
