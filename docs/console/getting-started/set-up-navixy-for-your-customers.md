---
description: >-
  Become a channel partner, create an organization and a workspace for each
  customer, and give your team access to them.
tags:
  - guide
  - beta
---

# Set up Navixy for your customers

This guide is for companies that run Navixy for other companies: resellers, system integrators, and telematics service providers. When you complete it, a customer will have their own organization with a workspace, products, data, and an administrator who can start working.

A customer organization works like a company of its own. It has its own users, roles, workspaces, and data, so the customer controls their setup and owns their data. You manage all your customers from your own organization, because each customer's organization is created under yours. Navixy calls such an organization a child organization.

This guide assumes that you have completed [Getting started](README.md) for your own company, so your organization, its first workspace, and your roles already exist.

## 1. Become a channel partner

A channel partner is an organization that can create child organizations. Navixy grants this status to your organization. You can't set it during registration or in Console.

To request it, write to <sales@navixy.com> with your organization's name and what you plan to do with it.

When your organization is a channel partner, you can create child organizations: the **Create organization** window has a **Child organization** option.

## 2. Set up tariff plans with Navixy

Every new workspace starts on the default plan. As a channel partner, you can build custom plans with Navixy for your own workspaces and your customers' workspaces.

To discuss plans, contact <sales@navixy.com>. All plans are in US dollars. The option to set other currencies for child organizations is planned and coming soon. Your own organization always receives its invoices in US dollars.

You can't choose or switch a workspace's tariff plan in Console, including on the [Billing](../billing/README.md) page. Contact Navixy to switch your workspaces to the plans that you set up. When you create a workspace for a customer, contact Navixy to switch that workspace too.

## 3. Create an organization for each customer

Create one child organization for each company that you work with. In the same window, you also set up the organization's first workspace, as you did when you registered.

To create a child organization, follow these steps:

{% stepper %}
{% step %}

### Select your organization

Sign in to Navixy Console. In the top navigation bar, select your own organization from the left dropdown menu.
{% endstep %}

{% step %}

### Open the Create organization window

On the sidebar, click **Organization**, then click **Create organization**.
{% endstep %}

{% step %}

### Name the organization

Select **Child organization** and enter an **Organization name**. Use your customer's company name. The window title changes to **Create child organization**.

If **Child organization** can't be selected, your organization isn't a channel partner yet. See [Become a channel partner](#id-1.-become-a-channel-partner).
{% endstep %}

{% step %}

### Decide whether the customer can create organizations

A channel partner can grant the channel partner status to a child organization without asking Navixy. A large channel partner uses it to add more levels: an organization for each branch, and under each branch, their customers' organizations.

Select **Channel partner** if the customer needs more levels under their organization. Leave it blank for end customers without child organizations.

Click **Continue**.
{% endstep %}

{% step %}

### Set up the first workspace

The next three steps set up the new organization's first workspace:

1. **Region**: Where the workspace's data is stored. Only **EU (Germany)** can be selected. The other regions are marked **Coming soon**.
2. **Deployment**: Keep the selected option. Options marked as **Legacy** can't be selected.
3. **Products**: The standard set of Navixy products is already selected and marked as **Included**. Select any other products that the customer needs. On the same step, check the **Workspace name**. By default, it's the organization's name. You can change it.

The **Launch preview** panel shows what you have chosen so far. Click **Continue** to move to the next step, and **Launch** on the last one.
{% endstep %}
{% endstepper %}

Console confirms that the organization is created and that its workspace is launching. Now you have:

- A child organization under yours. It appears under **Child organizations** in the left dropdown menu of the top navigation bar.
- The Owner role in the child organization, assigned to you.
- The Admin role in the child organization, assigned to nobody. It has full access, and you can give it to the customer's administrator. See [Roles that Navixy creates](../users-and-roles/README.md#roles-that-navixy-creates).
- The child organization's first workspace, on the default tariff plan.

To open the child organization, click **Go to organization**, or select it in the left dropdown menu.

{% hint style="danger" %}
Deleting an organization also deletes every organization under it. Plan your organization tree before you create the organizations in it.
{% endhint %}

## 4. Set up the customer's workspace

Prepare the workspace before the customer starts to use it: products, data, and a role for the customer's administrator.

To set up the customer's workspace, follow these steps:

1. In the top navigation bar, select the customer's organization from the left dropdown menu.
2. On the sidebar, click **Console hub**.
3. Check that the products that the customer needs are enabled: an enabled product has a green dot on its icon and an **Open** button.
4. Next to a product, click **Open**.
5. In the product, add the customer's data, such as the GPS devices and the vehicles that they track.
6. Choose a role for the customer's administrator. The Admin role gives full access to the customer's organization. To give less access, create a role. For the steps, see [Create a role](../users-and-roles/manage-roles.md#create-a-role).
7. Invite the customer's administrator with this role. For the steps, see [Invite a user](../users-and-roles/manage-users.md#invite-a-user).

An administrator's role manages the organization: its workspaces, users, and roles. If you create this role yourself, select the **Console** product for it.

## 5. Give your team access to the customer's organization

A role works only in the organization where it was created. A role in your own organization gives no access to a customer's organization, however wide the role is. Your organization's users also don't appear in a customer's organization, so you can't assign a role to them there until they join it.

To give a colleague access to the customer's organization, follow these steps:

1. In the top navigation bar, select the customer's organization from the left dropdown menu.
2. Create a role for your colleague. For the steps, see [Create a role](../users-and-roles/manage-roles.md#create-a-role).
3. Invite your colleague with this role. Enter the email address that your colleague uses to sign in to Navixy. For the steps, see [Invite a user](../users-and-roles/manage-users.md#invite-a-user).

When your colleague accepts the invitation, they are a member of both organizations. They switch between the two organizations in the left dropdown menu of the top navigation bar.

## 6. Hand over the organization to the customer

When you create a child organization, you become its Owner. You can stay the Owner if the customer agrees. The customer's administrator then works with the role that you gave them.

If the customer wants to own their organization, contact Navixy to transfer the Owner role to the customer's administrator. Transferring the Owner role in Console is planned and coming soon.

## 7. Give temporary access for one-time support

Sometimes, your employee might need to do some work in the customer's organization, for example, to change settings for the customer. Give this person only the access required by the task and remove it when the work is done.

To give temporary access, follow these steps:

1. In the top navigation bar, select the customer's organization from the left dropdown menu.
2. Create a role that covers only the support task. For the steps, see [Create a role](../users-and-roles/manage-roles.md#create-a-role) and [Configure what a role allows](../users-and-roles/manage-roles.md#configure-what-a-role-allows).
3. Invite the person with this role. Under **Security profile**, select **Limited** or **Protected**. For the steps, see [Invite a user](../users-and-roles/manage-users.md#invite-a-user).
4. After the person accepts the invitation, open the role's page and set an expiration date for them under **Assign to users**. For the steps, see [Set role expiration date](../users-and-roles/manage-users.md#set-role-expiration-date).

On that date, the role stops applying, and the person loses access without any action from you. If the work ends earlier, revoke the role. For the steps, see [Revoke a role](../users-and-roles/manage-users.md#revoke-a-role). The person loses access within about 5 minutes.

People with a stricter security profile will see a notice in Console that they work in another company's organization. This notice is planned and coming soon.

## 8. Start working

The customer's organization is ready for everyday work. The customer's team works with the products, and each workspace is billed according to its tariff plan.

Note that cross-organization billing is coming soon. Your child organizations' usage will be added to your own organization's usage and charged at your tariff plan's prices.

{% hint style="success" %}
Your customer's organization is set up. The customer's administrator can start working as soon as they accept their invitation.
{% endhint %}

## Next steps

- [Create organization](../organization/create-organization.md): Create another child organization or an independent organization.
- [Manage workspaces](../organization/manage-workspaces.md): Create more workspaces in the customer's organization.
- [Manage users](../users-and-roles/manage-users.md): Invite more people to a customer's organization and assign or revoke their roles.
- [Manage roles](../users-and-roles/manage-roles.md): Create, copy, and narrow roles in a customer's organization.
