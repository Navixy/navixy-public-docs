---
description: >-
  How to create an API key in Navixy Console, copy it, replace a key that was
  deleted or expired, and delete keys that are no longer needed.
tags:
  - how-to
  - beta
---

# Manage API keys

## Create an API key

An API key lets an app call Navixy APIs with one role of your organization. An app is any program that uses the key, such as a script or an integration. To create a key, you need the Owner role or a role that includes the **Console** and **IAM** products. The key gets no more access than your own roles give you. If your own access ends on a set date, the key needs an expiration date on or before that date. If you select a later date or leave **Never**, Console doesn't create the key and names the last date that you can select.

To create an API key, follow these steps:

1. Go to **API keys** and click **Create API key**. The **Create API key** window opens.
2. Enter a **Name**, up to 100 characters. Use the name of the app that uses the key.
3. Under **Role**, select the key's role. The list shows only active roles that cover the workspace selected in the top navigation bar and give no more access rights than your own roles.
4. (Optional) Under **Expiration date**, click **Never**. The **Set expiration date** window opens.
5. Select a date or a preset, such as **In 30 days**. The earliest date is tomorrow, and the key stops working at the start of the selected day. Without a date, the key never expires.
6. Click **Set date**.
7. Click **Create API key**. The window shows the key and an example request.
8. Click **Copy**.
9. Store the key where the app reads it, such as a secrets store or an environment variable.
10. (Optional) Click **Copy example** and pass the example to the app's developer.
11. Click **Done**.

Console shows the key only in this window. After you close it, nobody can see the key again: the list shows only its first characters and its last 4 characters. If you lose the key, delete it and create a new key. If you click **Done** before you copy the key, Console asks you to confirm. Copying the example doesn't count as copying the key.

The example request shows the app's developer how to exchange the key for an access token and send a first request to the Navixy GraphQL API. The example doesn't contain the key. It reads the key from the `NAVIXY_API_KEY` environment variable and names the workspace selected in the top navigation bar. The developer replaces the app name in the example with the name of their app. See [Authentication](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication) in the Navixy GraphQL API documentation.

Console deletes the key on its own if its role is deleted or your own roles change. See [When a key stops working](README.md#when-a-key-stops-working).

{% hint style="warning" %}
If the **Role** list says that no roles are available, no active role fits a key in the selected workspace. Create a role or select another workspace in the top navigation bar. Then click **Create API key** again. See [Create a role](../users-and-roles/manage-roles.md#create-a-role).
{% endhint %}

## Replace an API key

Replace a key when it was deleted, when it expired, or when it may have leaked. A key can't be edited or restored, so you create a new key and give it to the app. To create a key, you need the Owner role or any role that includes the **Console** and **IAM** products.

To replace an API key, follow these steps:

1. Create a new key with the role that the app needs. See [Create an API key](#create-an-api-key).
2. Put the new key into the setting that the app reads it from or pass the key to the people who run the app.
3. If the old key is still in the list, delete it. See [Delete an API key](#delete-an-api-key).

The app works again as soon as it signs in with the new key.

## Delete an API key

Delete a key when the app no longer needs it or when the key may have leaked. To delete a key, you need the Owner role or any role that includes the **Console** and **IAM** products. Your own roles must also give at least the access rights that the key's role gives. An expired key stays in the list until you delete it.

To delete an API key, follow these steps:

1. Go to **API keys**.
2. Click **Delete** next to the key. To delete several keys at once, select them and click **Delete** in the bar that appears at the bottom of the page.
3. In the confirmation window, click **Delete API key**. For several keys, click **Delete**.

The key disappears from the list, and nobody can restore it. The key can't sign in again. A session that the app started with the key before you deleted it keeps working until it ends, within a few minutes.
