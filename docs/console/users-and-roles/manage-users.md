---
description: >-
  How to invite users to an organization in Navixy Console, revoke an
  invitation, assign and revoke roles, set a role expiration date, remove a
  member, rename a member, and change the security profile of a member.
tags:
  - how-to
  - beta
---

# Manage users

## Invite a user

An invitation reserves a place in the organization for an email address and stores the roles that the person receives. To invite a user, you need the Owner role or a role that includes the **Console** and **Iam** products.

Every invitation includes at least one role. A new organization has the Admin role, which gives full access, so you can invite the people who manage the organization with you right away. To give a person less access, create a role for them first. See [Create a role](manage-roles.md#create-a-role). The Owner role can't be given in an invitation. See [Roles that Navixy creates](README.md#roles-that-navixy-creates).

To invite a user, follow these steps:

1. Go to **Users and roles** and click **Invite user**. The **Invite user** page opens.
2. In **Email addresses**, enter the address of each person that you want to invite. Put each address on its own line, or separate the addresses with commas. You can invite up to 100 addresses at once.
3. Under **Access**, select one or more **Roles**. Every person that you invite at once receives all the selected roles. The list shows only active roles that give no more access than your own roles.
4. Under **Product to open**, select the product that opens when the person accepts the invitation. The list shows the products that the selected roles give access to. Later sign-ins aren't affected.
5. Under **Security profile**, keep **Standard** for your own employees. For contractors and for people from other companies, select **Limited** or **Protected**.
6. Click **Send invitation**.

The page shows the result for each address. An address that already belongs to a member or already has a pending invitation can't be invited. The people that you invited appear under **Recently invited**. To invite more people, repeat steps 2 to 6. When you finish, click **Done**.

The invitation appears on the **Invitations** tab with the status **Pending**, and the person appears on the **Users** tab with the status **Invited**. Navixy sends the person an email with a link to accept the invitation. When they accept, their status changes to **Active**.

An invitation has an expiration date, shown under **Sent** on the **Invitations** tab. If the person doesn't accept it in time, its status changes to **Expired**. To give them access, invite them again.

{% hint style="warning" %}
If the page says that no roles are available, you can't select a role or send the invitation. The organization has no active role that you can give. Create a role, then click **Invite user** again. See [Create a role](manage-roles.md#create-a-role).
{% endhint %}

## Revoke an invitation

Revoke an invitation that was sent to a wrong address or to a person who no longer needs access. You can revoke only a pending invitation.

To revoke an invitation, follow these steps:

1. Go to **Users and roles** and open the **Invitations** tab.
2. Click **Revoke** next to the invitation.
3. In the **Revoke invitation** window, confirm.

The invitation's status changes to **Revoked**.

## Assign a role to a user

Assign a role when someone's work changes, or when the role that they were invited with no longer covers what they do. A role assigned this way has no expiration date. To end a role on a set date, see [Set role expiration date](#set-role-expiration-date). To assign a role, you need the Owner role or a role that includes the **Console** and **Iam** products.

A member can have several roles. To give one person an extra permission, assign them another role instead of changing a role that other people share.

To assign a role, follow these steps:

1. Go to **Users and roles** and open the **Users** tab.
2. Click the person's name to open their details.
3. Click <picture><source srcset="../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/add.svg" alt="Assign role" data-size="line"></picture> next to **Roles**. The **Assigned roles** window opens.
4. Under **Available roles**, click **Assign** next to the role. The list shows the roles that apply to the workspace selected in the top navigation bar.
5. In the confirmation window, click **Assign**.

The Owner role isn't offered, because only one person can have it.

## Set role expiration date

A role assignment can have an expiration date. On that date, the role stops applying, and the person loses the access that it gives without any action from you. Use it for contractors and for temporary work. To set or change the date, you need the Owner role or a role that includes the **Console** and **Iam** products. You set the date on the role's page, where you can also assign the role to several people at once. You can assign a role there only while one of its workspaces is selected in the top navigation bar. Otherwise, select one of the role's workspaces first.

To set a role expiration date, follow these steps:

1. Go to **Users and roles** and open the **Roles** tab.
2. Click <picture><source srcset="../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> next to the role. The role's page opens.
3. Under **Assign to users**, select the person.
4. Click **No expiration** next to the person and select the date. The earliest date that you can select is tomorrow.
5. Click **Save changes**.
6. In the confirmation window, click **Save changes**.

The **Expiration date** column on the **Users** tab shows the date. **Never** means that the assignment has no expiration date. To remove the date, click <picture><source srcset="../.gitbook/assets/icons/close-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/close.svg" alt="Clear expiration" data-size="line"></picture> next to it on the role's page and save the role.

## Revoke a role

Revoke a role when the person no longer needs the access that it gives. To revoke a role, you need the Owner role or a role that includes the **Console** and **Iam** products.

To revoke a role, follow these steps:

1. Go to **Users and roles** and open the **Users** tab.
2. Click the person's name to open their details.
3. Click <picture><source srcset="../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/add.svg" alt="Assign role" data-size="line"></picture> next to **Roles**. The **Assigned roles** window opens.
4. Under **Assigned roles**, click **Revoke** next to the role.
5. In the confirmation window, click **Revoke**.

You can also revoke a role on the role's page: under **Assign to users**, clear the person, save the role, and confirm.

The person loses access rights within about 5 minutes. A member whose last role is revoked stays in the organization without a role. To give them access again, assign them a role. You don't need to invite them again. The Owner role can't be revoked.

## Remove a member

Removing a member revokes all their roles in the organization, and they lose access to it. You can't undo it, but you can invite the person again. To remove a member, you need the Owner role or a role that includes the **Console** product. You can't remove a member whose roles give more access than yours. You also can't remove yourself, the organization's only Owner, or a person who belongs to no other organization.

To remove a member, follow these steps:

1. Go to **Users and roles** and open the **Users** tab.
2. Click the person's name to open their details.
3. Under **Remove member from organization**, click **Remove**.
4. In the confirmation window, click **Remove member**.

## Rename a member

You can change a member's first and last name, for example to fix a typo. The name is the person's own, so the new name shows in every organization that they belong to and on the sign-in pages. To rename a member, you need the Owner role or a role that includes the **Console** product. You can't rename a person who was removed from the organization, and you can't change the email address.

To rename a member, follow these steps:

1. Go to **Users and roles** and open the **Users** tab.
2. Click the person's name to open their details.
3. Change the **First name** and the **Last name**. Both are required, and each can be up to 255 characters long.
4. Click **Save**.

## Change a member's security profile

A security profile sets how strict the sign-in rules are for a member, such as how long a session lasts. A member who has none follows the **Standard** rules, and the **Users** tab shows **No security profile** for them. You select the profile when you invite the person, and you can change it later. Keep **Standard** for your own employees, and choose **Limited** or **Protected** for contractors and for people from other companies.

To change the security profile, follow these steps:

1. Go to **Users and roles** and open the **Users** tab.
2. Click the person's name to open their details.
3. Under **Security profile**, select a profile.

The **Security profile** column on the **Users** tab shows each member's profile.

**MFA** in the member's details shows whether the member uses multi-factor authentication. You can't change it there.
