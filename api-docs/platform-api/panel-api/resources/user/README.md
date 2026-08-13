---
description: API calls on work with users in the admin panel.
---

# User

In the Navixy Admin Panel, a User refers to the accounts of Organizations or Individuals who are customers of the Dealer (or Sub Dealer). For example, an organization 'ABC Inc.' can be a User with the type "legal\_entity," and John Doe can be a User with the type "individual."

> User accounts may have additional sub-accounts, commonly referred to as 'sub-users,' allowing larger organizations to grant access to multiple employees.

This page describes the User object and the API actions that can be performed with it within the Admin Panel.

## User Object Structure

{% openapi-schemas spec="admin-panel" schemas="User" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

The read operations return this flat form, with every legal field present regardless of `legal_type` and empty strings where a field does not apply.

The create and update operations take a different and stricter shape that varies by `legal_type`. It is documented as `UserEditForm` on those two operations below, and the three variants are cumulative: a sole trader is an individual plus registration details, and a legal entity is a sole trader plus a company name and interbranch code. The legal variants also tighten the postal fields, which may be empty for an individual but not for a sole trader or legal entity.

## Discount Object Structure

{% openapi-schemas spec="admin-panel" schemas="UserDiscount" grouped="true" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-schemas %}

## API actions

API path: `panel/user`.

***

_required permissions_: `users: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/user/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 206 - Login already in use - if this email is already registered.

***

_required permissions_: `users: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/user/read" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - when the user does not exist, is a sub-user, or belongs to another dealer.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/update" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if the specified user does not exist or belongs to a different dealer.
* 206 - Login already in use - if the specified `login` is used by another user.

***

_required permissions_: `users: "update"`.

{% openapi-operation spec="admin-panel" path="/panel/user/change_password" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `users: "corrupt"`.

{% openapi-operation spec="admin-panel" path="/panel/user/corrupt" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if the specified user does not exist or belongs to a different dealer.

***

_required permissions_: `users: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/user/upload" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Spreadsheet columns

CSV uses `;` as the column separator. Column headers are localized, and headers marked `*` are always required.

For an English-locale file:

`Email address*;Password*;Status*;Legal status*;Surname*;Name*;Middle name;Phone number;Comment;Country;Region;City;Street, address;Zip code;Legal name;Tax number;IEC;Registration country;Registration region;Registration city;Registration address;Registration zip code;Discount;End date of discount;Device limit`

For a Russian-locale file:

`Адрес электронной почты*;Пароль*;Статус*;Юридический статус*;Фамилия*;Имя*;Отчество;Номер телефона;Комментарий;Страна;Регион;Город;Улица, дом, квартира;Почтовый индекс;Юридическое название;ИНН;КПП;ОГРН;ОКПО;Страна регистрации;Регион регистрации;Город регистрации;Улица, дом регистрации;Почтовый индекс регистрации;Скидка;Дата окончания скидки;Минимальное число устройств для скидки`

`Legal status` must be one of the following numbers:

* 1 - individual
* 2 - legal entity
* 3 - sole trader

For legal entity (2) and sole trader (3), the following columns must also be present and filled with data, in addition to those marked `*`:

`Country;Region;City;Street, address;Zip code;Legal name;Registration region;Registration city;Registration address;Registration zip code`

`Legal name` is the exception: it is not required for a sole trader (3).

The remaining columns are optional and can be omitted. Columns can appear in any order.

New users are created with the time zone specified in the `default_user_time_zone` [service setting](../dealer/settings/service.md).

***

_required permissions_: `users: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/user/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `users: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/user/export" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

***

_required permissions_: `users: "read"` and `user_sessions: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/user/session/create" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 201 - Not found in the database - if the specified user does not exist or belongs to a different dealer.

***

_required permissions_: `users: "read"` and `transactions: "read"`.

{% openapi-operation spec="admin-panel" path="/panel/user/transaction/list" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}

#### Errors

* 211 - Requested time span is too big - if the interval between `from` and `to` exceeds the permitted maximum.

***

_required permissions_: `users: "update"` and `transactions: "create"`.

{% openapi-operation spec="admin-panel" path="/panel/user/transaction/change_balance" method="post" %}
[OpenAPI admin-panel](../../reference/Admin_Panel.json)
{% endopenapi-operation %}
