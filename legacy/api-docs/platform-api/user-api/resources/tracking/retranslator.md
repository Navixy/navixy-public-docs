---
title: Retranslator
description: Forward the data a device sends to the Navixy platform on to a third-party server.
---

# Retranslator

A retranslator forwards the data that a device sends to the platform on to a third-party server of the user's choosing, so the same stream can feed an outside application without touching the device.

Each retranslator names a protocol, the address and port of the receiving server, and whether it is currently enabled. Some protocols also require a login and a password. Which ones do is decided by the protocol, so call `retranslator/protocols/list` before creating a retranslator and read the credential rules it reports.

Every operation that changes a retranslator requires the `admin` right, which is available only to master users. Listing them and listing the protocols are not restricted.

## Retranslator protocol object

{% openapi-schemas spec="navixy-platform" schemas="RetranslatorProtocol" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## Retranslator object

{% openapi-schemas spec="navixy-platform" schemas="Retranslator" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-schemas %}

## API actions

API base path: `/retranslator`.

Operations that list no errors of their own return only the [general error codes](../../../general/errors.md#error-codes).

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/retranslator/create" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters, when the protocol requires a login or a password and the field was sent empty.
* 206 - Login already in use. For the Navixy Web Service protocol, another retranslator on the platform already uses this login.
* 247 - Entity already exists, when a retranslator with this address, port, and login already exists.
* 268 - Over quota, when the user's quota for retranslators is exceeded.

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/retranslator/delete" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 201 - Not found in the database.

***

{% openapi-operation spec="navixy-platform" path="/retranslator/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

***

_Required sub-user rights:_ `admin` (available only to master users).

{% openapi-operation spec="navixy-platform" path="/retranslator/update" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}

#### Errors

These errors come in addition to the [general error codes](../../../general/errors.md#error-codes):

* 7 - Invalid parameters, when the protocol requires a login or a password and the field was sent empty.
* 201 - Not found in the database, when no retranslator has the given ID or it belongs to another user.
* 206 - Login already in use. For the Navixy Web Service protocol, another retranslator on the platform already uses this login.
* 247 - Entity already exists, when a retranslator with this address, port, and login already exists.

***

{% openapi-operation spec="navixy-platform" path="/retranslator/protocols/list" method="post" %}
[OpenAPI navixy-platform](../../reference/Navixy_Platform.json)
{% endopenapi-operation %}
