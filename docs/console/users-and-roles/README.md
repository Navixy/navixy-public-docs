---
description: >-
  The Users and roles page of Navixy Console: the users, roles, and invitations
  of an organization, the user details, the role page, and security profiles.
tags:
  - reference
  - beta
noIndex: true
---

# Users and roles

A user is a person's Navixy account. It exists on its own, so the same person can be a member of several organizations. A person joins your organization through an invitation, which always gives them at least one role. If you later revoke all of a member's roles, they stay in the organization without access, and you can assign them a role again without a new invitation.

A role sets what its members can do: which workspaces they can access, which products they use, and what they can do with the data. A role applies only in the organization where it's created. A member can have several roles, and their access is the sum of those roles.

This page describes the **Users and roles** page, the user details, the role page, and invitations. To learn how to work with users and roles, see [Manage users](manage-users.md) and [Manage roles](manage-roles.md).

## Users and roles page

**Users and roles** is where you invite people, see who has which role, and create and edit roles. To open it, click **Users and roles** on the sidebar. The page shows the organization that you selected in the top navigation bar.

<figure><img src="../.gitbook/assets/users-and-roles-page.png" alt="The Users and roles page of Navixy Console. The top navigation bar has the organization and workspace dropdown menus and a search field. Below the title and its description are the buttons Create role and Invite user. Four cards follow: My roles and Products show your roles and the products they give you, and Data resources and MFA enabled show that their data is unavailable. Under the tabs Users, Roles, and Invitations, with a search icon next to them, the Users table has the columns User, Roles, Scope, Status, Security profile, Expiration date, Last sign-in, and MFA. One member has the Owner role and the status Active, and the invited people have the status Invited."><figcaption><p>The Users and roles page</p></figcaption></figure>

Here's what you can do:

* Create roles and invite people: Click **Create role** or **Invite user**. Each opens a page of its own, and <picture><source srcset="../.gitbook/assets/icons/arrow_back-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/arrow_back.svg" alt="Back to Users and roles" data-size="line"></picture> next to its title returns you to **Users and roles**.
* See your own access: The cards at the top show your roles and the products they give you. A card that Console can't fill shows **Unavailable**.
* Find users: **Users** lists the organization's members and invited people, with their roles, scope, status, security profile, expiration date, last sign-in, and multi-factor authentication. <picture><source srcset="../.gitbook/assets/icons/search-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/search.svg" alt="Search users" data-size="line"></picture> next to the tabs filters the list by name or email. Click a person's name to open their [details](./#user-details).
* Manage roles: **Roles** lists the organization's roles, with the number of users and products for each. Next to each role are <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture>, <picture><source srcset="../.gitbook/assets/icons/person_add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/person_add.svg" alt="Assign" data-size="line"></picture>, <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Duplicate" data-size="line"></picture>, and <picture><source srcset="../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/delete.svg" alt="Delete" data-size="line"></picture>. <picture><source srcset="../.gitbook/assets/icons/person_add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/person_add.svg" alt="Assign" data-size="line"></picture> is unavailable at the moment: assign roles on the role's page or in the user details. A role marked **System** can't be edited, copied, or deleted.
* Track invitations: **Invitations** lists the invitations with their roles, the date they were sent, when they expire, and their status. You can **Revoke** a pending invitation.

## User details

Clicking a person's name opens a side panel titled with their name or email. Its top shows the person's **User ID**, which <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy user ID" data-size="line"></picture> copies, when they last signed in under **Last sign-in**, when they joined under **Member since**, and the **Expiration date** of their role assignment. **Never** means that the assignment doesn't end. You set the date on the role's page.

| Section | Description |
| --- | --- |
| **First name**, **Last name** | The person's name. You can change it. See [Rename a member](manage-users.md#rename-a-member). |
| **Email** | The address that the person signs in with. You can't change it. |
| **Security profile** | The sign-in rules for the person. You can select another profile here. See [Security profiles](./#security-profiles). |
| **Workspaces** | The workspaces that the person's roles reach, such as **All workspaces**. |
| **Products** | The products that the person's roles give access to. |
| **Roles** | The person's roles. <picture><source srcset="../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/add.svg" alt="Assign role" data-size="line"></picture> opens the **Assigned roles** window, where you assign and revoke roles. |
| **MFA** | Whether the person uses multi-factor authentication. You can't change it here. |
| **Remove member from organization** | **Remove** takes the person out of the organization. See [Remove a member](manage-users.md#remove-a-member). |

A person invited to the organization has the status **Invited** until they accept the invitation, and **Active** after that. A member whose last role is revoked stays in the organization without a role. You can assign them a role later without inviting them again.

Devices, assets, asset groups, geo objects, and schedules belong to the workspace, not to the person who created them. When a member loses access, these records stay in the workspace, and nobody needs to move them.

Navixy keeps an audit log for each workspace. It records when devices, inventories, assets, asset groups, geo objects, schedules, custom fields, and catalog items are created, changed, or deleted, with who made the change, when, and which fields changed. A person stays the author of their changes after they lose access. The log doesn't include sign-ins and sign-outs, or changes to users and roles in Console. It also leaves out some links between records, such as adding an asset to a group or linking a device to an inventory. You read the log through the Navixy GraphQL API, as described in [Investigating changes with audit logs](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/products/business-data-repository/guides/investigating-changes-with-audit-logs). No Navixy app shows it at the moment.

## Role page

**Create role**, <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture>, and <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Duplicate" data-size="line"></picture> open the role's page. **Cancel** or <picture><source srcset="../.gitbook/assets/icons/arrow_back-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/arrow_back.svg" alt="Back to Users and roles" data-size="line"></picture> leaves it, and Console asks you to confirm if you have unsaved changes.

| Element                   | Description                                                                                                                                                                                                                                           |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**, **Description** | The role's name, which doesn't have to be unique, and who the role is for.                                                                                                                                                                            |
| **Active**                | On by default. A role that isn't active gives no access, but its members keep it.                                                                                                                                                                     |
| **Workspaces**            | **All** workspaces of the organization, including those created later, or **Specific** ones.                                                                                                                                                          |
| **Products**              | **All** products, or **Specific** ones that you select. The list shows the products of the plan of the workspace selected in the top navigation bar. A selected product gives full access: every feature and every data resource, current and future. |
| **Features**              | The features of a product that the role allows: **All** or **Specific**.                                                                                                                                                                              |
| **Resources**             | The types of data in a product. Each type gives access to **All** records or **Specific** ones, with the actions that the role allows, such as **View**, **Create**, **Edit**, and **Delete**.                                                        |
| **Assign to users**       | The organization's members. Selecting a person assigns the role, and clearing them revokes it when you save. The date next to a person sets when their assignment ends. **Assigned** lists only the people who have the role. You can select new people only while one of the role's workspaces is selected in the top navigation bar. |

{% hint style="warning" %}
A role that manages the organization, its workspaces, and its users needs the **Console** product. To create, edit, delete, assign, or revoke roles, to change when an assignment expires, or to invite users, the role needs the **Iam** product as well. Without **Console**, Console refuses every request from the people who have the role, including read-only requests. Without **Iam**, they can't do any of these things.
{% endhint %}

You can't grant more access permissions than you have yourself. Console refuses to save a role with more access than your own roles give you.

## Roles that Navixy creates

Navixy creates two roles in every new organization: Owner and Admin.

The Owner role is a system role. Navixy assigns it to the person who created the organization. It has full access to the organization and all its workspaces. Only one person can have it, so it isn't offered when you invite people or assign roles. Nobody can edit, copy, delete, or revoke it. Transferring the Owner role in Console is planned and coming soon. Until then, contact Navixy to transfer it.

The Admin role has the same full access as the Owner role: every product and every workspace. It's an ordinary role, so you can give it in an invitation, assign it, edit it, copy it, and delete it. Navixy doesn't assign it to anyone, and doesn't create it again after you delete it. Use it for the people who manage the organization with you.

## Invitations

An invitation reserves a place in the organization for an email address and stores the roles that the person receives. Every invitation includes at least one role. The person gets an email with a link to accept it.

| Status       | Description                                                                                                                      |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| **Pending**  | Sent and not accepted yet. You can revoke it.                                                                                    |
| **Accepted** | The person joined the organization.                                                                                              |
| **Expired**  | The person didn't accept it in time. An invitation expires 7 days after it's sent. To give the person access, invite them again. |
| **Revoked**  | Revoked before the person accepted it.                                                                                           |

## Security profiles

A security profile sets how strict the sign-in rules are for a member, such as how long a session lasts. It doesn't change what the member can reach, because their roles decide that. You select it when you invite the person. If a member has no security profile set, the **Users** tab shows **No security profile** for them, and Navixy applies the **Standard** sign-in rules. When a session ends, the member signs in again.

| Profile       | For                                                                | Sign-in rules                                                                                                                                                               |
| ------------- | ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Standard**  | Your own employees                                                 | A session lasts up to 12 hours. A single sign-in step, such as a password, is enough.                                                                                       |
| **Limited**   | Contractors that you trust                                         | A session lasts up to 8 hours. Signing in also needs a one-time code from an authenticator app. A member who hasn't set up an app is asked to set one up when they sign in. |
| **Protected** | People from outside your company who work in the organization once | A session lasts up to 4 hours. Signing in also needs a one-time code from an authenticator app, the same as for **Limited**.                                                |
