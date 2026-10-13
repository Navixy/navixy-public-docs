---
description: >-
  What you can see and do in Location Intelligence depends on your workspace's
  products and your roles. Learn what a role limits in each product.
tags:
  - fundamentals
  - beta
noIndex: true
---

# Access and roles

What you can do in Location Intelligence depends on two things: the products that your workspace includes, and the roles that you have. The people who set up your workspace decide both. This page explains what each of them limits, what you see when something isn't allowed, and what a role can limit in each product. It's for everyone who works in Location Intelligence.

{% hint style="info" %}
**Not sure who manages your account?** It's someone in your company or at your service provider. [Your account administrator](your-account-administrator.md) helps you find out who it is and who to contact about access, devices, and help.
{% endhint %}

## Workspace products and roles

**The products of the workspace** are the same for everyone who works in it. A product that the workspace doesn't include isn't available to anyone there, whatever their role.

**A role** decides what one person can do. It decides the following:

* Which workspaces you can open
* Which products you can use
* Which records you can see, such as all the assets of the workspace or only some of them
* What you can do with these records: view, create, edit, or delete them

You can have several roles. Your access is everything that your roles allow together. A role can also be assigned to you until a set date. On that date, the role stops giving you access. The date belongs to your assignment of the role, not to the role itself, so it doesn't change the access of other people who have the same role.

Your roles can give you different access in each workspace. When you switch to another workspace, the products and the records that you see can change.

## What you see when something isn't allowed

Location Intelligence shows what you have access to and leaves out the rest. An action that your role doesn't allow fails with an error.

| Situation | What you see |
| --- | --- |
| Your workspace doesn't include a product | The product isn't in the menu. |
| Your roles don't include Reports or Dashboards | The product isn't in the menu, even when your workspace includes it. |
| Your roles don't give you access to the data of another product | The product is in the menu when your workspace includes it. When you open it, it shows an error message and no data. |
| Your role lets you see only some records | Lists, maps, and counts show only these records. A record that you can see doesn't show the linked records that you can't see. For example, an asset doesn't show its GPS device if your role doesn't let you see the device. |
| Your role doesn't allow an action | The button for the action is shown, such as **Create** or **Delete**. When you click it, an error message appears and nothing changes. |
| None of your roles covers a workspace | The workspace can still be in the workspace list. Switching to it fails with the message "Failed to switch workspace". |

## How to get more access

If you need access that you don't have, ask the people who set up your workspace. Tell them the workspace, the product, and what you tried to do. They're the ones who change your roles and the products of your workspace.

If you aren't sure who they are, see [Your account administrator](your-account-administrator.md). It explains how to find out whether someone in your company or your service provider manages your account.

## What a role can limit in each product

A role gives access to a product as a whole, or to parts of it. The table shows how far a role can limit each product.

| Product | What a role can limit |
| --- | --- |
| [Business Data Repository](bdr/README.md#access-to-business-data-repository): **Assets**, **Geo objects**, **Schedules** | Which records you can see and what you can do with them, separately for each kind of record: assets, asset groups, geo objects, schedules, devices, inventories, and the types with their custom fields |
| [Device Management](device-management/README.md#access-to-device-management) | The devices and inventories that you can see and what you can do with them. Device Management follows your access to devices in Business Data Repository |
| [Tracking](tracking/README.md#access-to-tracking) | The assets and devices that you see on the map. Tracking follows your access to assets and devices in Business Data Repository |
| [IoT Logic](iot-logic/flow-management/README.md#access-to-flows) | What you can do with flows: view, create, edit, or delete them. Access applies to all the flows of the workspace. Which devices you can add to a flow follows your access to devices |
| [Reports](reports/README.md#access-to-reports) | Which report types you can see, and which of them you can run |
| [Dashboards](dashboards/README.md#access-to-dashboards) | Only the product as a whole |

Reports and Dashboards show the data of all the assets of the workspace. A role that lets you see only some assets in **Assets** doesn't limit the assets in a report or on a dashboard.

## When a change to your access applies

A change to your roles or to the products of your workspace doesn't apply at once. It applies within about 5 minutes. To apply it sooner, sign out and sign in again.
