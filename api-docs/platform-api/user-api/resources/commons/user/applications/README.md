---
title: User applications
description: Third-party applications embedded into the Navixy platform interface, and how to manage them through the API.
---

# User applications

A user application embeds a third-party application into the platform interface, either inline or opened in a new tab. It is configured entirely from the account, with no developer or support involvement, using URL parameters, user authentication, and session management to pass context through to the embedded application.

Applications are usually configured from the interface. For those instructions, see [User applications in the user documentation](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/account/user-applications). The operations below do the same configuration from code: they list, create, replace, delete, and hide applications.

## Application object

{% openapi-schemas spec="navixy-platform" schemas="UserApplication" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/user/application`.

Operations that list no errors of their own return only the [general error codes](../../../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ none.

{% openapi-operation spec="navixy-platform" path="/user/application/list" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/user/application/create" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 268 - Over quota, when the account already holds as many applications as the platform allows.

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/user/application/update" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when `id` names no application of this account, or is left out.

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/user/application/delete" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when `application_id` names no application of this account, or is left out.

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/user/application/enabled/set" method="post" %}
[OpenAPI navixy-platform](../../../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../../../general/errors.md#error-codes):

* 201 - Not found in the database, when `application_id` names no application of this account, or is left out.

## How to authenticate custom applications

An embedded application can use Navixy's own authentication rather than asking users to sign in again, through the **Authentication Gateway**. The gateway sits between the two, so a user already signed in to Navixy reaches the embedded application already authenticated, handled on the backend.

The gateway gives an application:

* Automatic user authentication through Navixy sessions.
* Database access credentials for IoT and user data.
* Session management and token-based API authorization.

Custom dashboards, analytics tools, and business system integrations are the usual cases. For the implementation details, see [Authentication Gateway](app-connect.md).
