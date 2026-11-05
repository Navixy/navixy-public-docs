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

An API key lets a script, a tool, or an app call Navixy GraphQL API on behalf of your organization. The key doesn't work with the RESTful Navixy API, which has keys of its own. An app is any program that uses the key, written by your developers or a contractor. The key signs in on its own with a single role that you choose, so the app doesn't need your entire account. API keys have many uses, such as creating an integration that exports trips to your accounting system or building software for your dispatchers.

This document describes the **API keys** page, what a key can do, and when a key stops working. To learn how to create, replace, and delete keys, see [Manage API keys](manage-api-keys.md).

## API keys page

**API keys** lists the organization's keys with their roles and expiration dates. To open it, click **API keys** on the sidebar. The page shows the keys of the organization that you selected in the top navigation bar.

<figure><img src="../.gitbook/assets/api-keys-page.png" alt="The API keys page of Navixy Console. The top navigation bar has the organization and workspace dropdown menus and a search field. Below the title and its description is the Create API key button. The list has a search icon, a filter icon, and the columns Name, Role, Expiration, Created, Last sign-in, and Key. Each row has a Delete action. Three keys are listed: one never expires, one has expired, and one has an expiration date. None of them has signed in, so Last sign-in shows a dash. Under the list are the Export to CSV and View API request icons."><figcaption><p>The API keys page</p></figcaption></figure>

Here's what you can do:

* Create keys: Each key gets one role and an optional expiration date. Console shows the key only once, right after it's created, together with an example request for the app's developer. See [Create an API key](manage-api-keys.md#create-an-api-key).
* Find keys: <picture><source srcset="../.gitbook/assets/icons/search-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/search.svg" alt="Search keys" data-size="line"></picture> finds keys by name or by the visible part of the key, and <picture><source srcset="../.gitbook/assets/icons/filter_list-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/filter_list.svg" alt="Filter keys" data-size="line"></picture> narrows the list by role and expiration. The list shows active and expired keys. Deleted keys aren't listed.
* Check whether a key is in use: Each key shows who created it and when it last signed in. A key that has never signed in shows a dash.
* Export keys: <picture><source srcset="../.gitbook/assets/icons/download-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/download.svg" alt="Export to CSV" data-size="line"></picture> under the list downloads the keys that match the filters as a CSV file.
* View the API request: **View API request**, the { } button under the list, shows the request that lists the organization's keys through the Navixy GraphQL API, with links to the authentication guide and the Console API reference. The request accepts a member's access token, not a token that an app got with an API key.
* Delete keys: You can delete one key or several keys at once. A deleted key can't be restored. See [Delete an API key](manage-api-keys.md#delete-an-api-key).

| Column               | Description                                                                                                                                                             |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**             | The name given to the key. The audit log identifies the key by this name.                                                                                               |
| **Role**             | The key's role. If the role was deleted, the column says that the role is no longer available. Console deletes the key when its role is deleted, so this state is short. |
| **Expiration**       | The day when the key stops working, or **Never**. The key stops working at the start of that day. An expired key shows **Expired** with its date and stays in the list until you delete it.                                              |
| **Created**          | When the key was created and who created it.                                                                                                                            |
| **Last sign-in**     | When the key last signed in, with a delay of up to a minute.                                                                                                            |
| **Key**              | The key's first characters and its last 4 characters. Nobody can see the whole key after it's created.                                                                  |

## What a key can do

A key has one role and receives its permissions: the same workspaces, products, and records as a member with that role. A key can't get more access rights than the person who created it, but the access limits set for that person don't apply to the key. To change what a key can do, edit its role or create a new key with another role and delete the old key. You can't change a key's role or expiration date after you create it.

A key works in one workspace at a time among the workspaces available to its role. A session starts each time the app that uses the key signs in with it, and the app names the workspace for that session. If the role reaches only one workspace, the app doesn't need to name it. To work in another workspace, the app signs in again and starts a new session.

A key is treated as a member of your organization, but it doesn't appear on the **Users** tab of **Users and roles**, and you can't assign it a second role. The **Roles** tab shows how many keys have each role, in the **API keys** column. Deleting a role deletes its keys, and Console says how many before you confirm. In the log, a key's changes appear under the key's name.

You don't choose a security profile for a key. Every key gets the same profile, which shows only in the CSV export and in the API. The profile sets how long an access token lasts, and it's the same for every key. The key's role decides its access rights, and its expiration date decides how long it works. A key never needs the one-time code from an authenticator app, because no person is there to enter it.

## When a key stops working

A key stops signing in when it expires, when you delete it, or when Console deletes it because its access rights changed. A session starts each time the app signs in with the key, and a key that stops signing in doesn't end the sessions that already started. Each session keeps working until it ends. The exception is a deleted role: when Console deletes a key because its role was deleted, the sessions that already started lose the role's access, usually within seconds. No session lasts past the key's expiration date. After that, every request from the app fails until the app gets a new key.

Console deletes a key on its own in these cases:

* The key's role is deleted.
* Someone revokes the key's role through the IAM API, with `memberRoleRevoke`.
* A role is assigned to the person who created the key or revoked from them.
* One of the creator's role assignments expires or gets another expiration date.
* A role that the creator has is edited or deleted.
* The creator leaves the organization.

Nobody can give a key more access than their role has, and Console keeps this true after the key is created. Console doesn't compare the old and the new access. Any change to the creator's roles deletes their keys, including a change that gives them more access. Console also checks every 15 minutes that each key's access still matches the records in IAM and deletes a key whose records changed. Editing the key's role doesn't delete the key, unless the creator has that role too. The key gets the role's new access, like a member would.

Console records every deletion with its reason in the [audit log](../audit-log/README.md), as an event on the key. Console doesn't send a notification about a deletion, so the first sign of a deleted key is often that the app's requests fail.

## Apps that depend on a key

An app built on a key must accept a new key. A key belongs to the person who created it. Console deletes it when that person's access changes, even when an app depends on the key. Console can't move a key to another person.

The app must read its key from a setting: a configuration value, an environment variable, or a field on its settings screen. The key must not be written into the app's code. After Console deletes the key, a member who can create keys creates a new key with the role that the app needs. The people who run the app put the new key into the setting, and the app works again without a code change. See [Replace an API key](manage-api-keys.md#replace-an-api-key).

If a contractor builds the app, write this requirement into their task and check it on delivery. When the contractor leaves your organization or their roles change, Console deletes every key that they created, including the app's key.

## See also

* [Manage API keys](manage-api-keys.md): Create, replace, and delete API keys.
* [Authentication](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication) in Navixy GraphQL API: Learn how an app exchanges a key for an access token and renews it.
* [Users and roles](../users-and-roles/README.md): Learn how roles work.
