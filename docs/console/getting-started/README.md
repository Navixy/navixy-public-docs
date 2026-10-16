---
description: >-
  Register with Navixy, check your first workspace's products, add your data,
  invite the people who work with you, and create roles for other access.
tags:
  - guide
  - beta
noIndex: true
---

# Getting started

This onboarding guide takes you from the registration form to an organization your colleagues can work in. It's written for the person who registers first, because that person becomes the Owner and decides what everyone else may do. It's also agent-friendly.

Each step names what Navixy creates for you, so you know what already exists before you configure anything. You don't need anything except an email address. Registering is the first thing you need to do to start working with Navixy.

The guide ends when your team can start working. If you also run Navixy for other companies, complete this guide for your own company first, then continue with [Set up Navixy for your customers](set-up-navixy-for-your-customers.md).

## 1. Register

Registration takes an email address and a few choices: your organization name, your first workspace name, and the products you need. It ends with you signed in to Navixy Console.

To create a Navixy Console account, follow these steps:

{% stepper %}
{% step %}
**Open the registration page**

Go to the [Navixy registration page](http://console.navixy.com/signup?utm_source=navixy-docs\&utm_campaign=console-getting-started).
{% endstep %}

{% step %}
**Enter your email address**

Enter your email address and click **Continue**.
{% endstep %}

{% step %}
**Verify your email address**

Navixy sends a message to the address you entered. Open it and click the link. The **Email verification** page tells you where the message went and lets you send it again.
{% endstep %}

{% step %}
**Set a password**

On the **Create your password** page, enter a **Password** of at least 12 characters and repeat it in **Confirm password**. The password must differ from your email address. Click **Continue**.
{% endstep %}

{% step %}
**Name your organization**

An organization is your company in Navixy. It owns the users, the roles, and the workspaces you create under it.

Enter an **Organization name**. Use your company's name. It is how your organization appears to everyone you invite. You can change it later.

**Home region** shows where your organization's data is stored. It's where your data is kept, not where your company operates.

{% hint style="info" %}
Navixy stores data in three regions: the European Union, the United States, and Saudi Arabia. The **Home region** field shows **European Union** and can't be changed, so your organization and all its workspaces store their data there.
{% endhint %}
{% endstep %}

{% step %}
**Set up your workspace**

A workspace stores your business data, such as devices and assets. Each workspace has its own products and tariff plan, so you can use separate workspaces for separate fleets, teams, or departments.

The **Set up your workspace** page has three steps. The **Launch preview** panel beside them shows what you have chosen so far.

1. **Region**: Where the workspace's data is stored. **EU (Germany)** is selected. The other regions are marked **Coming soon** and can't be selected.
2. **Profile**: Keep the selected profile. Profiles marked as **Legacy** can't be selected.
3. **Products**: The standard set of Navixy products. They are already selected and marked as **Included**, so you don't need to change anything. Enter a **Workspace name** on this step too. It's required. Every organization needs at least one workspace, and you can add more later. The workspace starts on the default tariff plan.

Click **Continue** to move to the next step and **Launch** on the last one.
{% endstep %}
{% endstepper %}

Registration is complete. Now you have:

* A Navixy user record: Your identity on the platform. It exists on its own, so the same user can later belong to several organizations.
* An organization with the name you entered and its home region.
* The Owner role, assigned to you. It has full rights in the organization and all its workspaces, and nobody can edit or delete it.
* The Admin role, assigned to nobody. It has the same full rights as the Owner role, and you can give it to the people you invite. See [Roles that Navixy creates](../users-and-roles/README.md#roles-that-navixy-creates).
* One workspace with its name, region, and the standard set of products.
* A subscription for that workspace to the default tariff plan.

If you want to create another workspace, see [Create a workspace](../organization/manage-workspaces.md#create-a-workspace).

{% hint style="warning" %}
If the page **Setting up your organization** appears instead, the setup service didn't respond, but your account and your organization name were saved. Click **Retry setup**. The **Set up your workspace** page opens again. Check your choices, enter the **Workspace name**, and click **Launch**.

If you close the browser before you finish, sign in again. Registration resumes at the step where you stopped. Your password and your organization name are kept, but you make the choices on the **Set up your workspace** page again.
{% endhint %}

## 2. Check your products in the Console hub

After **Launch**, Console opens on the Console hub. The top of the hub shows your workspace name, organization, region, and tariff plan. To come back to the hub later, click **Console hub** on the sidebar.

While Navixy creates your workspace, the hub tells you that instead of listing products. This usually takes less than a minute, and the hub updates automatically.

Under **My products**, each product shows its status. A product shows as **Enabling** while Navixy sets it up, which takes a few minutes. When the product is ready, a green dot appears on its icon, and the **Open** button appears next to it.

The products that your plan requires stay enabled. You can enable or disable the other products with **Enable** and **Disable**. Disabling a product removes user access to it, but its data is kept. To use a product not covered by your plan, contact [sales@navixy.com](mailto:sales@navixy.com).

## 3. Set up your product data

Your workspace is empty after registration. Add your business data in the products before your team starts: without GPS devices and assets, the products have nothing to track and nothing to report on.

To add your data, follow these steps:

1. On the sidebar, click **Console hub**.
2. Next to a product under **My products**, click **Open**. The product opens in a new tab, in the same workspace.
3. Add your data in the product, such as your GPS devices, the vehicles that they track, and the places that matter to your work.

## 4. Invite a user

An invitation reserves a place in the organization for one email address and stores the roles the person receives. Every invitation includes at least one role.

Your organization already has the Admin role. It gives full access to every product and workspace, so you can invite the colleagues who manage the organization with you right away. The Owner role can't be given in an invitation, because only one person can have it. For people who need less access, create a role in the next step and invite them with it.

To invite a user, follow these steps:

1. Go to **Users and roles** and click **Invite user**. The **Invite user** page opens.
2. Enter the **Email addresses** to invite, one per line or separated by commas.
3. Under **Access**, select one or more **Roles** that every invited person receives, such as **Admin**.
4. Under **Product to open**, select the product that opens when the person accepts the invitation. The list shows the products that the selected roles give access to. Later sign-ins aren't affected.
5.  Under **Security profile**, keep **Standard** for your own employees, or select **Limited** or **Protected** for contractors.

    <div data-gb-custom-block data-tag="hint" data-style="info" class="hint hint-info"><p>A security profile sets how strict the sign-in rules are for that person, such as how long a session lasts. To change it after the person accepts the invitation, open their details on the <strong>Users</strong> tab and select a profile under <strong>Security profile</strong>.</p></div>
6. Click **Send invitation**, then click **Done**.

The invitation appears on the **Invitations** tab with the status **Pending**, and the person appears on the **Users** tab with the status **Invited**. They become a member when they accept, and their status changes to **Active**.

To accept, the person should click the link in the invitation email. A person without a Navixy account will fill in their profile and create a password on the **Set up your Console account** page, and a person with an account will sign in. Either way, the product selected under **Product to open** will open, without the registration steps.

An address that already belongs to a member of the organization or already has a pending invitation won't receive another invitation.

## 5. Create a role for other access

A role is what an organization grants to a user. It sets which workspaces the person can access, which products they use, and what they may do with the data. A role applies only in the organization where it's created.

Most people need less access than the Admin role gives, such as a dispatcher who sees only one depot's vehicles. Create a role that gives only the access they need.

To create a role, follow these steps:

{% stepper %}
{% step %}
**Open the Create role page**

Go to **Users and roles** and click **Create role**.

To start from an existing role instead, such as Admin, open the **Roles** tab and click <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Duplicate" data-size="line"></picture> next to it. Console opens a copy with **Copy** added to its name.
{% endstep %}

{% step %}
**Name the role**

Under **Details**, enter a **Name** and add a **Description** that says who the role is for. Role names don't have to be unique.
{% endstep %}

{% step %}
**Choose the workspaces**

Set **Workspaces** to **All**, or choose **Specific** and select at least one workspace.

**All** also covers workspaces created later, so a role set to **All** doesn't need editing when a new workspace appears.
{% endstep %}

{% step %}
**Select the products available to the role**

Under **Products**, select each product that the role gives access to. A new role has no products selected. Selecting a product grants full access to it: every feature and every data resource, current and future.
{% endstep %}

{% step %}
**Configure the access**

Click <picture><source srcset="../.gitbook/assets/icons/chevron_right-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/chevron_right.svg" alt="Set access by feature and resource" data-size="line"></picture> next to a selected product. Under **Resources**, set each data type to **All** or **Specific**, and select the actions that the role allows, such as **View**, **Create**, **Edit**, and **Delete**.

Some products have **Features**. Set them to **All** or **Specific**. **All** means current and future items.
{% endstep %}

{% step %}
**Save the role**

Click **Save role**. The role becomes active immediately. There is no separate publishing step.

You can also assign the role to members of the organization on this page, under **Assign to users**. To give the role to someone who isn't a member yet, invite them with it.
{% endstep %}
{% endstepper %}

{% hint style="warning" %}
A role that manages the organization, its workspaces, and its users needs the **Console** product. To create, edit, delete, assign, or revoke roles, to change when an assignment expires, or to invite users, the role needs the **Iam** product as well. Without **Console**, Console refuses every request from the people who have the role, including read-only requests. Without **Iam**, they can't do any of these things.
{% endhint %}

You can't grant more access than you have yourself. A role with wider access than your own is rejected when you save it.

When the role is saved, invite the people who need it. Follow the steps in [4. Invite a user](#id-4.-invite-a-user), and select this role under **Roles**.

## 6. Assign a role to a member

Assign a role when someone's work changes, or when the role they were invited with no longer covers what they do. Roles are assigned on the **Users** tab, not on the **Roles** tab.

A member can have several roles. To give one person an extra permission, assign them another role instead of changing a role that other people share.

To assign a role, follow these steps:

1. Go to **Users and roles** and open the **Users** tab.
2. Click the person's name to open their details.
3. Click **Assign role**. The **Assigned roles** window opens.
4. Select a role under **Available roles** and click **Assign**.

To revoke a role, click **Revoke** next to it under **Assigned roles**.

{% hint style="info" %}
Console records a revocation straight away. The person loses the access rights within about 5 minutes.
{% endhint %}

A member whose last role is revoked stays in the organization without a role, so you can assign them a role later without inviting them again. Console doesn't ask you to confirm a revocation.

A role assignment can have an expiration date. When the date passes, the person loses the access that the role gives. To set the date, open the role's page. See [Set role expiration date](../users-and-roles/manage-users.md#set-role-expiration-date).

## 7. Start working

Your organization is ready for everyday work. Your team works in the products, and each workspace is billed according to its tariff plan.

Every month from its subscription date, a workspace will receive an invoice for its usage in that month. To see a workspace's plans, balance, and balance history, click **Billing** on the sidebar. See [Billing](../billing/).

{% hint style="info" %}
Every new workspace starts on the default tariff plan. If you need products that it doesn't cover, contact [sales@navixy.com](mailto:sales@navixy.com), and Navixy will build a plan for your organization.
{% endhint %}

{% hint style="success" %}
Congratulations! Your organization is set up, and your onboarding is complete. Everyone you invited can start working in the products as soon as they accept their invitation.
{% endhint %}

## Next steps

* [Set up Navixy for your customers](set-up-navixy-for-your-customers.md): Create an organization and a workspace for each company that you work with, if you run Navixy for other companies.
* [Manage workspaces](../organization/manage-workspaces.md): Create more workspaces for other fleets, teams, or departments.
* [Create organization](../organization/create-organization.md): Create another organization, or one for each customer if you are a channel partner.
* [Manage users](../users-and-roles/manage-users.md): Invite more people, revoke invitations, assign and revoke roles, and change security profiles.
* [Manage roles](../users-and-roles/manage-roles.md): Configure what a role allows, copy a role, and edit or delete roles.
