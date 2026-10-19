---
description: >-
  How to create a workspace in Navixy Console, switch between workspaces,
  enable or disable a product, rename a workspace, and delete a workspace.
tags:
  - how-to
  - beta
---

# Manage workspaces

## Create a workspace

Create another workspace when a fleet, a team, or a department needs its own business data, products, or tariff plan. A workspace belongs to one organization, and its data isn't visible from that organization's other workspaces. To create a workspace, you need the Owner role or a role that includes the **Console** product.

The **Create workspace** window has three steps: **Region**, **Deployment**, and **Products**. Click **Continue** to move to the next step, and **Launch** on the last one.

To create a workspace, follow these steps:

1. In the top navigation bar, select your organization from the left dropdown menu.
2. On the sidebar, click **Organization**, then click **Create workspace**.
3. Select a **Region**. Only **EU (Germany)** can be selected. The other regions are marked **Coming soon**.
4. Keep the selected **Deployment** option.
5. Select the **Products** that the workspace needs. The products marked as **Included** are always selected.
6. On the same step, enter a **Workspace name**.
7. Click **Launch**.

The workspace starts on the default tariff plan. On the **Workspaces** tab of the **Organization** page, its status reads **Requested**, then **Creating** while Navixy sets it up, and **Active** when it's ready to use. If you need a different plan, contact <sales@navixy.com>.

## Switch to another workspace

Console works in one workspace at a time. The Console hub, the products, and their data all belong to the workspace that you selected.

To switch to another workspace, follow these steps:

1. In the top navigation bar, open the right dropdown menu.
2. Under **Switch workspace**, select the workspace.

Each workspace in the list shows its region, and its status while it isn't active yet. Console stays on the page that you were on and shows it for the selected workspace.

## Enable or disable a product

A workspace's tariff plan decides which products you can use in it. You can enable or disable the products that the plan covers, except for the products required by the plan, which stay enabled. To enable or disable a product, you need the Owner role or a role that includes the **Console** product.

To enable a product, follow these steps:

1. In the top navigation bar, select the workspace from the right dropdown menu.
2. On the sidebar, click **Console hub**.
3. Under **My products**, click **Enable** next to the product.

The product shows **Enabling** while Navixy sets it up, which takes a few minutes, and **Enabled** when it's ready.

To disable a product, follow these steps:

1. In the top navigation bar, select the workspace from the right dropdown menu.
2. On the sidebar, click **Console hub**.
3. Under **My products**, click **Disable** next to the product.
4. In the confirmation window, click **Disable**.

Users lose access to the product until you enable it again. Its data is kept.

To use a product that the workspace's plan doesn't cover, contact <sales@navixy.com> to change the plan.

## Rename a workspace

You can change a workspace's name and description. The name appears in the right dropdown menu of the top navigation bar and in every list of workspaces.

To rename a workspace, follow these steps:

1. On the sidebar, click **Organization**.
2. On the **Workspaces** tab, click the workspace's name. The workspace details open.
3. Change the **Workspace name** and the **Description**.
4. Click **Save**.

## Delete a workspace

Deleting a workspace can't be undone. An organization must keep at least one workspace, so you can't delete the last one. To delete a workspace, you need the Owner role or a role that includes the **Console** product.

To delete a workspace, follow these steps:

1. On the sidebar, click **Organization**.
2. On the **Workspaces** tab, click the workspace's name. The workspace details open.
3. Under **Delete workspace**, click **Delete**.
4. In the confirmation window, click **Delete workspace**. The window says that the workspace's data and products stop being available to everyone in it, and that you can't undo this.

The workspace shows the status **Terminating** and stays in the list until Navixy finishes deleting it.
