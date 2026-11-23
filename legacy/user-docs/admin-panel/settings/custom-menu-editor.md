---
description: >-
  Create menu presets to rename/reorder items, add/remove tabs, add your own
  applications, and assign custom menus to specific users
tags:
  - legacy
---

# Menu editor

**Menu editor** is a tool that allows owners to control what their end users can see. It provides more consistent and standard user experience and access control. With this feature, admins can reorder and rename tabs as well as group users with specific builds.

<figure><img src="../.gitbook/assets/Untitled-20240811-031917 (1).gif" alt="Working with Menu editor"><figcaption><p>Working with Menu editor</p></figcaption></figure>

## How to create a menu template

Navigate to **Settings → Menu editor**. You will see this screen, or a variation of it:

<figure><img src="../.gitbook/assets/image (49) (1).png" alt="Menu editor"><figcaption><p>Menu editor</p></figcaption></figure>

The page consists of the following sections:

* **Menu presets**
* **Assigned to** (displayed only for non-default presets)
* **Visual editor**
* **Item selector**

### **Menu presets**

A list of created menu presets. By default, this only contains the default template created by Navixy.

To create a new preset, click <img src="../.gitbook/assets/image (42).png" alt="Add new preset" data-size="line"> and enter the name you wish. To duplicate an existing preset, select it in the drop-down menu and click <img src="../.gitbook/assets/image (43) (1).png" alt="Duplicate preset" data-size="line">. To rename it, click <img src="../.gitbook/assets/image (47) (1).png" alt="Rename preset" data-size="line">. To delete it, click <img src="../.gitbook/assets/image (44) (1).png" alt="Delete preset" data-size="line">. Clicking <img src="../.gitbook/assets/image (45) (1).png" alt="Star" data-size="line"> makes the preset the default for every user who has no preset of their own. The Admin Panel asks you to confirm first, because the default preset replaces these users' menus. Menu items that the preset doesn't contain disappear from them.

<figure><img src="../.gitbook/assets/image (46) (1).png" alt="Selecting a preset" width="267"><figcaption><p>Selecting a preset</p></figcaption></figure>

### **Assigned to**

This section is used for applying the template to specific users.

Presets can be applied to any chosen user. If you delete a preset, the user is assigned the template marked as default. When you add users to a preset, the Admin Panel asks you to confirm, because the preset replaces their menus.

### **Visual editor**

An interactive editor of your side menu. As items are rearranged or renamed, you see the changes both in the menu and in the **Item selector** section on the right of the page.

All menu items can be renamed and dragged-and-dropped to order them as you see fit. Tabs inside each module can also be renamed and moved.

<figure><img src="../.gitbook/assets/image (50) (1).png" alt="Visual editor"><figcaption><p>Visual editor</p></figcaption></figure>

### **Item selector**

This section shows a preview of the item you select or add to the menu and allows you to add new items to your groups.

To add a tab to the selected menu item, click **Add item** at the top of the section and select the tab from the drop-down list. To add a new menu item, click **Add item** in a menu group in the **Visual editor** section. You can add multiple items to a menu group.

The drop-down list has two groups:

* **Menu items**: The sections of the Navixy platform, such as tracking or reports.
* **Custom apps**: Your own web applications from the [Applications](applications.md) tab. The list shows only the applications that are turned on.

An application can appear only once in each preset. To learn what happens to your presets when you turn off or delete an application, see [Applications](applications.md).

<figure><img src="../.gitbook/assets/image (52) (1).png" alt="Adding items"><figcaption><p>Adding items</p></figcaption></figure>

## API

For instructions on using the API, see [Menu preset](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/panel-api/resources/user/preset) in Developer documentation.
