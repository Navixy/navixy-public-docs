---
description: >-
  How to create a role in Navixy Console, configure what it allows, start from a
  copy of an existing role, and edit or delete a role.
tags:
  - how-to
  - beta
---

# Manage roles

## Create a role

A role sets which workspaces a person can access, which products they use, and what they can do with the data. A role applies only in the organization where it's created. To create a role, you need the Owner role or a role that includes the **Console** product.

A new organization already has the Admin role, which gives full access. Create a role when a person needs less access than that. See [Roles that Navixy creates](README.md#roles-that-navixy-creates).

To create a role, follow these steps:

1. Go to **Users and roles** and click **Create role**. The **Create role** page opens.
2. Enter a **Name**. Role names don't have to be unique.
3. (Optional) Enter a **Description** that says who the role is for.
4. Under **Workspaces**, keep **All**, or select **Specific** and select at least one workspace. **All** also covers workspaces created later.
5. Under **Products**, select each product that the role gives access to.
6. (Optional) Narrow the access to each product. See [Configure what a role allows](#configure-what-a-role-allows).
7. (Optional) Under **Assign to users**, select the people who receive the role. To end a person's access on a set date, click **No expiration** next to them and select the date.
8. Click **Save role**.

The role becomes active immediately. There is no separate publishing step.

**Products** lists the products of the plan of the workspace that you selected in the top navigation bar, together with the products that administer Navixy itself, such as **Console**. Selecting a product grants full access to it: every feature and every data resource, current and future. To give access to every product, set **Products** to **All**. If you select no product, the page warns that the role allows nothing. You can still save the role, but it gives no access until you edit it.

{% hint style="warning" %}
Some roles manage the organization itself: its workspaces, users, and roles. Select the **Console** product for that role. Without it, Console refuses every request from the people who have the role, including read-only requests.
{% endhint %}

You can't grant more access than you have yourself. If a role gives more access than your own roles, Console refuses to save it.

## Configure what a role allows

By default, a product in a role gives access to everything in the product. Narrow it when a person needs only some features, only some types of data, only some actions, or only some records.

To configure the access to a product, follow these steps:

1. Open the role's page: click **Create role**, or click <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> next to a role on the **Roles** tab.
2. Under **Products**, click <picture><source srcset="../.gitbook/assets/icons/chevron_right-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/chevron_right.svg" alt="Set access by feature and resource" data-size="line"></picture> next to a selected product. Its features and resources appear.
3. If the product has **Features**, set them to **All** or **Specific**. With **Specific**, select the features that the role allows.
4. Under **Resources**, find each type of data in the product. Set it to **All** or **Specific**. With **Specific**, select the records that the role gives access to. If the records belong to groups, you can group the list with **Group by** and select a whole group.
5. For each type, select the actions that the role allows, such as **View**, **Create**, **Edit**, and **Delete**.
6. Click **Save role** or **Save changes**.

**All** means current and future items. A role set to **All** doesn't need editing when a new feature or record appears. **Specific** is available only for the types of data whose records the product can list.

## Start from a copy of an existing role

A copy saves time when a new role differs from an existing role in only a few settings.

To copy a role, follow these steps:

1. Go to **Users and roles** and open the **Roles** tab.
2. Click <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Duplicate" data-size="line"></picture> next to the role. The **Create role** page opens with that role's settings and **Copy** added to its name.
3. Change the name and the settings that you need.
4. Click **Save role**.

The copy has no users. Assign it under **Assign to users** before you save, or later. You can also copy a role marked **System**, although the role itself can't be edited.

## Edit a role

A change to a role applies to everyone who has it. To give one person more access, assign them another role instead. See [Assign a role to a user](manage-users.md#assign-a-role-to-a-user).

To edit a role, follow these steps:

1. Go to **Users and roles** and open the **Roles** tab.
2. Click <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> next to the role. The role's page opens.
3. Change the settings that you need.
4. Click **Save changes**.

Under **Assign to users**, the role's page also shows who has the role. **Assigned** lists only them. Selecting or clearing a person and saving assigns or revokes the role.

Roles marked **System** are created by Navixy and can't be edited. Their row on the **Roles** tab says that they aren't editable.

## Delete a role

Deleting a role removes the access that it gives from everyone who has it.

To delete a role, follow these steps:

1. Go to **Users and roles** and open the **Roles** tab.
2. Click <picture><source srcset="../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/delete.svg" alt="Delete" data-size="line"></picture> next to the role.
3. In the **Delete role** window, click **Delete**.

Roles marked as **System** can't be deleted.
