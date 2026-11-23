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

What you can do in Location Intelligence depends on 2 things: the products that your workspace includes and the roles that you have. Your account administrator decides both. A workspace is a separate set of assets, devices, and other items that your team works with. This page explains what products and roles limit, what you see when something isn't allowed, and what a role can limit in each product. It's for everyone who works in Location Intelligence.

{% hint style="info" %}
Not sure who your account administrator is? It's someone in your company or at your service provider. See [Who manages your workspace](who-manages-your-workspace.md) to find out who it is and who to ask about devices and help.
{% endhint %}

## Workspace products and roles

The workspace's products are the same for everyone who works in it. A product that the workspace doesn't include isn't available to anyone there, whatever their role.

A role decides what one person can do. It decides the following:

* Which workspaces you can open
* Which products you can use
* Which assets, devices, and other items you can see, such as all the workspace's assets or only some of them
* What you can do with these items: view, create, edit, or delete them

You can have several roles. Your access is everything that your roles allow together. A role can also be assigned to you until a set date. On that date, the role stops giving you access. The date belongs to your assignment of the role, not to the role itself. It doesn't change the access of other people who have the same role.

Your roles can give you different access in each workspace. When you switch to another workspace, the products, assets, devices, and other items that you see can change.

## What you see when something isn't allowed

Location Intelligence hides most of what you don't have access to. Some things stay visible, and when you use them, an error message appears.

| Situation | What you see |
| --- | --- |
| Your workspace doesn't include a product | The product isn't in the menu. |
| Your roles don't include Reports or Dashboards | The product isn't in the menu, even when your workspace includes it. |
| Your roles don't give you access to another product's data | The product is in the menu when your workspace includes it. When you open it, it shows an error message and no data. |
| Your roles let you see only some assets, devices, or other items | Lists, maps, and counts show only these items. An item that you can see doesn't show the linked items that you can't see. For example, an asset doesn't show its GPS device if your roles don't let you see the device. |
| Your roles don't allow an action | You see the button for the action, such as **Create** or **Delete**. When you click it, an error message appears and nothing changes. |
| None of your roles gives you access to a workspace | The workspace isn't in the workspace list. If it's still shown, for example, because your roles changed after the list loaded, switching to it fails with an error message. |

## How to get more access rights

If you need access that you don't have, ask your account administrator. Tell them the workspace, the product, and what you tried to do. They change your roles and your workspace's products.

If you aren't sure who your account administrator is, see [Who manages your workspace](who-manages-your-workspace.md). It explains how to find out whether your account administrator works for your company or for your service provider.

## What a role can limit in each product

A role gives access to a product as a whole or to parts of it. The table shows what a role can limit in each product.

| Product | What a role can limit |
| --- | --- |
| [Business Data Repository](bdr/README.md#access-to-business-data-repository): **Assets**, **Geo objects**, **Schedules** | Which items you can see and what you can do with them, separately for each kind: assets, asset groups, geo objects, schedules, devices, inventories, and the types with their custom fields |
| [Device management](device-management/README.md#access-to-device-management) | The devices and inventories that you can see and what you can do with them. Device management shows only the devices that you can see in Business Data Repository |
| [Tracking](tracking/README.md#access-to-tracking) | The assets and devices that you see on the map. Tracking shows only the assets and devices that you can see in Business Data Repository |
| [IoT Logic](iot-logic/flow-management/README.md#access-to-flows) | What you can do with flows: view, create, edit, or delete them. Access applies to all the workspace's flows. You can add to a flow only the devices that you can see |
| [Notification rules](rules-and-notifications/README.md#access-to-notification-rules) | What you can do with rules: view, create, edit, or delete them. Access applies to all the workspace's rules. You get notifications only about the assets that you can see |
| [Reports](reports/README.md#access-to-reports) | Which report types you can see, and which of them you can run. A report includes only the assets and devices that you can see in Business Data Repository |
| [Dashboards](dashboards/README.md#access-to-dashboards) | Which dashboards you can open. A dashboard shows only the assets, devices, and geo objects that you can see in Business Data Repository |

## When a change to your access applies

A change to your roles or to your workspace's products applies within about 5 minutes. To apply it sooner, sign out and sign in again.
