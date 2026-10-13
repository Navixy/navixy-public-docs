---
description: >-
  The Organization page of Navixy Console: the summary, workspaces, child
  organizations, and the organization and workspace details.
tags:
  - reference
  - beta
noIndex: true
---

# Organization

An organization is your company in Navixy. It has your users, your roles, and your workspaces. Everyone that you invite joins the organization, and the roles that you create apply only inside it.

A workspace is the place that stores your business data: devices, assets, geo objects, and the products that work with them. Each workspace has its own products and tariff plan, and its data isn't visible from the organization's other workspaces. Use separate workspaces for separate fleets, teams, or departments.

An organization that is a channel partner can also have child organizations, one for each company it works with. See [Set up Navixy for your customers](../getting-started/set-up-navixy-for-your-customers.md).

This page describes the **Organization** page, organization details, and workspace details. To learn how to work with organizations and workspaces, see [Create organization](create-organization.md) and [Manage workspaces](manage-workspaces.md).

## Organization page

**Organization** is where you see a summary of your organization, create workspaces and organizations, and open the details of the organization and its workspaces. To open it, click **Organization** on the sidebar. The page shows the organization that you selected in the top navigation bar.

<figure><img src="../.gitbook/assets/organization-page.png" alt="The Organization page of Navixy Console for an organization called Fleet Logistics. The top navigation bar has two dropdown menus, for organization and workspace, and a search field. Below, the organization name with the status Active and the role owner, a See details link, and the buttons Create workspace, Create organization, and Change owner, which is unavailable. Four summary cards follow: Members with 3 members and an Invite a user link, Assets and Billing with no data, and Workspaces with 1. Under the tabs Child organizations and Workspaces, the Workspaces table lists one workspace with 3 products, the region, the status, and its creation date."><figcaption><p>The Organization page</p></figcaption></figure>

Here's what you can do:

- Switch organizations and workspaces: The left dropdown menu of the top navigation bar selects the organization you work in, and the right one selects the workspace. The workspace list shows only the workspaces that your roles give access to. Both are available on every page of Console. The menu icon next to each dropdown copies the ID or the name of the organization or workspace.
- Create workspaces and organizations: Click **Create workspace** or **Create organization**. **Change owner** is unavailable: transferring the Owner role in Console is planned and coming soon.
- See a summary of the organization: The header shows the organization's status and your role in it. **Members** counts the current members and the pending invitations, and **Invite a user** opens the **Users** tab of **Users and roles**. **Assets** counts the tracked assets, **Billing** shows the balance and the plan for new workspaces, and **Workspaces** counts the workspaces. A card that Console can't fill says that its data is unavailable.
- Browse workspaces and child organizations: **Workspaces** lists the organization's workspaces with their products, region, status, and creation date. Click a workspace's name to open its [details](#workspace-details). **Child organizations** lists the organizations under yours with their users, devices, assets, balance, plan, and status.
- Open the organization details: Click **See details**.

## Organization details

**See details** opens a side panel titled with the organization's name and **Edit organization**. Its top shows the organization ID, with <picture><source srcset="../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/content_copy.svg" alt="Copy organization ID" data-size="line"></picture>, the currency, and the creation date.

The **General** tab has the following fields:

| Field                                             | Description                                                                                                                                                                 |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Display name**                                  | The organization's name, as everyone that you invite sees it.                                                                                                               |
| **URL**                                           | The organization's short name in addresses. Navixy sets it when the organization is created. You can't change it.                                                          |
| **Brand name**                                    | Your brand name, if it differs from the organization's name.                                                                                                                |
| **Growth manager**, **Contract type**, **Region** | Set by Navixy. You can't change them. **Region** is the organization's home region where its data is stored.                                                                |
| **Language**, **Country**                         | The organization's language and country.                                                                                                                                    |
| **Country code**, **Phone number**                | The organization's contact phone number.                                                                                                                                    |
| **Notes**                                         | Any text for your team.                                                                                                                                                     |
| **Channel partner**                               | Shows whether the organization can create child organizations. Navixy grants this status to your organization. A channel partner can grant it to their child organizations. |

The **Billing details** tab lists the organization's legal name and address, its tax ID or VAT number, bank details, and billing email. You can't edit these fields.

**Delete organization** deletes the organization and every organization under it. You can't delete your last organization.

## Workspace details

Clicking a workspace's name on the **Workspaces** tab opens a side panel titled with that name. Its top shows the workspace **ID**, the date it was **Created**, the **Organization**, and the **Tariff plan**. Below are the workspace's fields, including its **Region**, which you can't change, and its products.

You can change the **Workspace name** and the **Description**. See [Rename a workspace](manage-workspaces.md#rename-a-workspace). **Delete workspace** deletes the workspace, which can't be undone. An organization must keep at least one workspace, so you can't delete the last one. See [Delete a workspace](manage-workspaces.md#delete-a-workspace).

A new workspace shows the status **Requested**, then **Creating** while Navixy sets it up, and **Active** when it's ready to use. If a workspace shows **Failed**, contact Navixy.
