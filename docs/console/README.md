---
description: >-
  Navixy Console is where you run your company in Navixy: organizations,
  workspaces, products, and who can access them.
tags:
  - overview
  - beta
noIndex: true
---

# Navixy Console

<figure><img src=".gitbook/assets/image (1).png" alt="The Console hub for a workspace of an organization. The top navigation bar has the organization and workspace dropdown menus and a search field. Under the workspace name, a line names the organization, the region, and the tariff plan. My products lists five products: four with a green status dot and the buttons Disable and Open, and one with a red status dot and an Enable button. Below is Recent activity."><figcaption><p>The Console hub</p></figcaption></figure>

A fleet business runs on several Navixy products at once: one stores the vehicles and people, one processes what the devices send, others turn it into reports and dashboards. Someone decides which teams get which products, who may see which data, and what it all costs. Navixy Console is where you make those decisions, for your own company and for every company you serve.

<p align="center"><a href="http://console.navixy.com/signup?utm_source=navixy-docs\&#x26;utm_campaign=console-overview" class="button primary" data-icon="right-to-bracket">Create a Console account</a></p>

## What you can do in Navixy Console

**Set up Navixy the way your company is organized.** In Navixy, your company is an organization. Its business data, such as vehicles, drivers, and depots, is stored in workspaces: one for the whole company or one for each fleet, depot, or branch. A workspace's data stays separate from the other workspaces.

**Pay for each workspace's usage.** Each workspace has its own tariff plan, which decides the products that it can use and what they cost. Tariff plans are flexible: Navixy assembles each plan from the parts that your business needs and builds custom plans on request. To learn what a plan can include or switch a workspace to another plan, contact [sales@navixy.com](mailto:sales@navixy.com).

**Run every product from one place.** Navixy is made of products, and each product does one job. For example, Business Data Repository stores your vehicles, people, and places, IoT Logic processes what the GPS devices send, and Reports and Dashboards turn the data into answers. Each workspace has a Console hub that lists its products. Enable the products covered by your plan and open any of them in one click. The AI Assistant, which answers questions about your organizations and workspaces next to the page you are on, will guide you on your journey in Navixy.

**Give every person the exact access they need.** The people who work with you join your organization through roles. A role decides which workspaces, products, and records a person can reach, down to single records: a dispatcher sees one depot's vehicles, and a fleet manager sees all of them. Nobody can grant more access than they have. Security profiles set stricter sign-in rules for contractors and for people from other companies. A role assignment can end on a set date, so that a contractor's access ends without anyone having to cancel it.

**Serve your customers from your own organization.** If you run Navixy for other companies, Navixy can make your organization a channel partner. You can then create an organization for each customer, called a child organization, with its own users, data, and administrator, and manage all of them from your account. A major partner adds levels for their branches and customers. When your support staff need to work in a customer's organization, they can get access for that task only. Billing across organizations is planned and coming soon: your child organizations' usage will be charged at your tariff plan's prices.

**Connect your own systems.** Your developers work with the same organizations, workspaces, and products through the Navixy APIs. API keys that you create and revoke in Console are planned and coming soon.

## Who it is for

Navixy Console is for the people who set up Navixy and decide who works with it:

* The Owner and the administrators of a company that runs its own fleet.
* Channel partners: telematics service providers, resellers, and system integrators who run Navixy for their customers.

The rest of the team uses Console less often. Most people open their products from the Console hub or directly and work there.

## Core concepts

Every product works inside a workspace, and every workspace belongs to one organization. People reach the workspaces through the roles of that organization.

* **Organization:** Your company in Navixy. It has your users, your roles, and your workspaces. A channel partner's organization can also have child organizations, one for each customer.
* **Workspace:** The place that stores your business data, such as devices, assets, and geo objects, together with the products that work with it. Each workspace has its own tariff plan, and its data isn't visible from the other workspaces.
* **Product:** A part of the Navixy platform that you enable in a workspace, such as Business Data Repository, IoT Logic, or Reports.
* **Tariff plan:** Decides which products a workspace can use and what they cost. Every new workspace starts on the default plan. Navixy builds custom plans on request. To switch a workspace to another plan, contact [sales@navixy.com](mailto:sales@navixy.com).
* **User and member:** A user is a person's Navixy account. A member is a user who belongs to an organization. A member's roles decide what they can access, and a member without roles can access nothing. The same person can be a member of several organizations.
* **Role:** Which workspaces, products, and records members can access. The Owner role, automatically received by the person who created the organization, has full access to everything within their organization. Every new organization also has the Admin role, with the same full access, to give to the people who manage the organization with you.
* **Security profile:** The sign-in rules for one member.
* **Channel partner:** An organization that can create child organizations. This status is granted by Navixy.

## Where to start

[Getting started](getting-started/) takes you from registration to an organization with a workspace, product data, and colleagues who can start working. If you run Navixy for other companies, continue with [Set up Navixy for your customers](getting-started/set-up-navixy-for-your-customers.md).

## Section contents

* [Getting started](getting-started/): Register, add your data, invite your colleagues, and create roles for other access.
  * [Set up Navixy for your customers](getting-started/set-up-navixy-for-your-customers.md): Create an organization and a workspace for each customer, and give your team access.
* [Organization](organization/): Learn how organizations, workspaces, and child organizations work.
  * [Create organization](organization/create-organization.md): Create an independent organization or a child organization.
  * [Manage workspaces](organization/manage-workspaces.md): Create, switch, rename, and delete workspaces, and enable or disable their products.
* [Users and roles](users-and-roles/): Learn how users, roles, invitations, and security profiles work.
  * [Manage users](users-and-roles/manage-users.md): Invite people, assign and revoke roles, set role expiration dates, remove members, and change security profiles.
  * [Manage roles](users-and-roles/manage-roles.md): Create, configure, copy, edit, and delete roles.
* [Billing](billing/): Learn what the Billing page shows about a workspace's plans and balance.
