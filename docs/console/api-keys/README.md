---
description: >-
  The API keys page of Navixy Console: what an API key is, page content,
  what a key can do, and when a key stops working.
tags:
  - reference
  - beta
noIndex: true
---
# API keys

An API key lets an AI agent, an app, or an integration call Navixy GraphQL API on behalf of your organization. API keys are the main way for AI agents to sign in to Navixy, and integrations and apps sign in the same way. The key doesn't work with the RESTful Navixy API, which has keys of its own. The key signs in on its own with a single role that you choose, so the app or agent doesn't need your entire account. API keys have many uses, such as giving an AI agent access to your fleet data, creating an integration that exports trips to your accounting system, or building software for your dispatchers.

{% hint style="info" %}
**For AI agents:** To work with Navixy under your own name, ask a person with access to Console to create an API key for you. Then exchange the key for an access token, as described in [Authentication](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication) in Navixy GraphQL API.
{% endhint %}

This document describes the **API keys** page, what a key can do, and when a key stops working. To learn how to create and delete keys, see [Manage API keys](manage-api-keys.md).

## API keys page

**API keys** lists the organization's keys with their attributes. To open it, click **API keys** on the sidebar. The page shows the keys of the organization that you selected in the top navigation bar.

<figure><img src="../.gitbook/assets/api-keys-page.png" alt="The API keys page of Navixy Console. The top navigation bar has the organization and workspace dropdown menus and a search field. Below the title and its description is the Create API key button. The list has a search icon, a filter icon, and the columns Name, Role, Expiration, Created, Last sign-in, and Key. Each row has a Delete action. Three keys are listed: one never expires, one has expired, and one has an expiration date. None of them has signed in, so Last sign-in shows a dash. Under the list are the Export to CSV and View API request icons."><figcaption><p>The API keys page</p></figcaption></figure>

Here's what you can do:

* Create keys: Each key gets one role and an optional expiration date. You can copy the key only once, in the window that opens right after you create it. Copy it right away: after you close the window, the key can't be copied, and you have to create a new one. The window also has an example request for the app's developer. See [Create an API key](manage-api-keys.md#create-an-api-key).
* Find keys: <picture><source srcset="../.gitbook/assets/icons/search-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/search.svg" alt="Search keys" data-size="line"></picture> finds keys by name or by the visible part of the key, and <picture><source srcset="../.gitbook/assets/icons/filter_list-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/filter_list.svg" alt="Filter keys" data-size="line"></picture> narrows the list by role and expiration. The list shows active and expired keys. Deleted keys aren't listed.
* Check whether a key is in use: Each key shows who created it and when it last signed in. A key that has never signed in shows a dash.
* Export keys: <picture><source srcset="../.gitbook/assets/icons/download-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/download.svg" alt="Export to CSV" data-size="line"></picture> under the list downloads the keys that match the filters as a CSV file.
* View the API request: **View API request**, the **{}** button under the list, shows the request that lists the organization's keys through the Navixy GraphQL API, with links to the authentication guide and the Console API reference. The request needs an access token issued to a person. Only people manage keys, so a token that an app got with an API key can't list, create, or delete keys.
* Delete keys: You can delete one key or several keys at once. A deleted key can't be restored. See [Delete an API key](manage-api-keys.md#delete-an-api-key).


| Column           | Description                                                                                                                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Name**         | The name given to the key. The audit log identifies the key by this name.                                                                                                                  |
| **Role**         | The key's role. It's the same kind of role that you assign to users, and it decides what the app can do with the key. See [Users and roles](../users-and-roles/README.md).                  |
| **Expiration**   | The day when the key stops working, or **Never**. The key stops working at the start of that day. An expired key shows **Expired** with its date and stays in the list until you delete it. |
| **Created**      | When the key was created and who created it.                                                                                                                                               |
| **Last sign-in** | When the key last signed in, with a delay of up to a minute.                                                                                                                               |
| **Key**          | The key's first characters and its last 4 characters. Nobody can see the whole key after it's created.                                                                                     |

## What a key can do

A key has one role and receives its permissions: the same workspaces, products, and records as a member with that role. A key can't get more access rights than the person who created it.

You can't change a key's role or expiration date after you create it. To give an app other access rights, delete its key and create a new key, with another role if needed. Create a separate key for each task, and don't use one key for different tasks.

A key is treated as a member of your organization, but it doesn't appear on the **Users** tab of **Users and roles**, and you can't assign it a second role. The **Roles** tab shows how many keys have each role, in the **API keys** column. In the log, a key's changes appear under the key's name.

## When a key stops working

A key stops working in any of these cases:

* The key expires.
* Someone deletes the key.
* The key's role is deleted. The **Delete role** window says how many keys the role has before you confirm.
* Someone revokes the key's role through the IAM API, with `memberRoleRevoke`.
* A role is assigned to the person who created the key or revoked from them.
* One of the creator's role assignments expires or gets another expiration date.
* A role that the creator has is edited or deleted.
* The creator leaves the organization.

Nobody can give a key more access than their role has. Any change to the roles of the person who created the key deletes all their keys, even a change that gives that person more access. This is a security measure. A key can't get more access than its creator has, and nobody can keep using a key after the creator's access rights change.

An expired key can't be renewed, and a deleted key can't be restored. The only way to keep the app working is to create a new key.

A session starts each time the app signs in with the key. A key that stops working doesn't end the sessions that already started. Each session keeps working until it ends. The exception is a deleted role: the sessions that already started lose the role's access, usually within seconds. No session lasts past the key's expiration date. After that, every request from the app fails until the app gets a new key.

Console records every deletion with its reason in the [audit log](../audit-log/README.md), as an event on the key. Console doesn't send a notification about a deletion, so the first sign of a deleted key is often that the app's requests fail.

## Apps that depend on a key

An app built on a key must accept a new key. A key belongs to the person who created it. Console deletes it when that person's access changes, even when an app depends on the key. Console can't move a key to another person.

The app must read its key from a setting: a configuration value, an environment variable, or a field on its settings screen. The key must not be written into the app's code. After Console deletes the key, a member creates a new key with the role that the app needs. The people who run the app put the new key into the setting, and the app works again without a code change. See [Give an app a new key](manage-api-keys.md#give-an-app-a-new-key).

Console doesn't stop anyone from building an app that works with one key only. So whoever sets the task for the app must require that it accepts a new key, and check it on delivery. For example, a contractor builds the app and leaves your organization when the work is done. Console deletes every key that they created, including the app's key. A member creates a new key, and the app keeps working with it.

## See also

* [Manage API keys](manage-api-keys.md): Create and delete API keys, and give an app a new key.
* [Authentication](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication) in Navixy GraphQL API: Learn how an app exchanges a key for an access token and renews it.
* [Users and roles](../users-and-roles/README.md): Learn how roles work.
