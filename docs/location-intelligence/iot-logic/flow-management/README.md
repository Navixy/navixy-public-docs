---
description: "The IoT Logic flow list: what each column shows, and how to create, edit, enable, export, and delete a flow."
tags:
  - reference
  - beta
---

# Flow management

The IoT Logic start page lists every flow in your workspace and is where you create, open, and remove them.

## Created flows

The table lists every flow in the workspace.

| Column | What it shows |
| --- | --- |
| **Flow name** | The name you gave the flow |
| **Last updated** | When the flow was last saved |
| **Connected devices** | How many devices enter the flow through its Data Source nodes |
| **Status** | **Enabled** when the flow is switched on and processing data, **Disabled** when it isn't. The switch in this column turns a flow on or off without opening it |

A flow with the status **Disabled** isn't processing anything. Its devices still send data, and the data still reaches the Navixy platform without the values that this flow adds.

## Creating and opening flows

| Control | What it does |
| --- | --- |
| **Create flow** | Opens a form for the flow's name and description |
| **Upload flow** | Opens a file that was downloaded from another flow. The file opens on the canvas as a new flow, which you then save. See [Downloading and uploading a flow](#downloading-and-uploading-a-flow) |
| **Mapping table** | Opens **Device mapping**, a list of every device connected to a flow, with the flows that it enters. See [Device mapping](#device-mapping) |

Click a row to open that flow on the canvas.

## The flow form

The same form appears when you create a flow and when you edit its settings.

| Field | What it does |
| --- | --- |
| **Flow name** | Required. What the flow is called in the list. 3 to 255 characters from a standard English keyboard: accented and Cyrillic letters are refused when the flow is saved |
| **Description (optional)** | Up to 4,000 characters. Use it to record what the flow is for and who maintains it |
| **Flow enabled** | Switches processing on. While it is off, this flow doesn't process its devices. On by default for a new flow |

{% hint style="info" %}
**Save** on the **Create flow** form opens an empty canvas. The flow is created the first time you save it there, and it appears in the list from then on. With **Flow enabled** on, that first save also switches the flow on. Saving checks the whole flow. See [Why flows won't save](../troubleshooting/flow-wont-save.md).
{% endhint %}

## Flow templates

The **Flow templates** gallery above **Created flows** offers prepared flows for common cases. A template gives you a flow that you then connect to your own devices and adjust, which is faster than building the same flow by hand. See [Flow templates](templates.md).

## Device mapping

**Mapping table** opens **Device mapping**, a list of every device that is selected in a flow of the workspace. It shows which flows a device is in without opening the **Data Source** node of every flow.

| Column | What it shows |
| --- | --- |
| **Name** | The device's name |
| **Model** | The device model's code |
| **Device identifier** | The device's identifier |
| **Connected flows** | The flow that the device is in. Click the flow's name to open it |
| **Data Source** | The **Data Source** node through which the device enters that flow |

A device has one row for each **Data Source** node that it is selected in, so a device in two flows has two rows. Search the list by device name, model, or identifier. The list shows **No devices are connected to flows yet.** when no flow has any devices.

## Row actions

Each row has a download icon, which saves the flow to a file, like **Download** in the context menu. The context menu of each row has three actions.

| Action | What it does |
| --- | --- |
| **Edit** | Opens the flow's settings form |
| **Download** | Saves the flow to a file, which **Upload flow** can turn back into a flow |
| **Delete** | Removes the flow permanently |

Use **Download** to copy a flow between workspaces and to keep a version of a flow before you change it.

{% hint style="warning" %}
Deleting a flow can't be undone. From that moment, the flow no longer processes data from its connected devices. The devices themselves don't change and keep sending data to the platform.
{% endhint %}

## Access to flows

What you can do with flows depends on your role in the workspace. A role can give you full access to the flows, or allow only some actions: viewing, creating, editing, or deleting flows. For example, a role can let you view flows without changing them. Switching a flow on or off counts as editing it.

Your access applies to all the flows of the workspace. A role can't give access to some flows only.

A flow shows only the devices that your role lets you see. The **Connected devices** count and the **Data Source** device lists leave out the other devices. You can add to a flow only the devices that you can see.

**Create flow**, **Upload flow**, and the actions in each row's context menu are shown to everyone. If your role doesn't allow the action, an error message appears when you try it. To get more access, ask the people who set up your workspace. To learn more, see [Access and roles](../../access-and-roles.md).

## Downloading and uploading a flow

**Download** saves a flow to a JSON file named after the flow. It is in each row's context menu and as the download icon in each row, and in the toolbar's context menu when the flow is open in the editor. You can download only a saved flow. The file contains the last saved version of the flow, so save your changes before you download it.

The file keeps the flow's structure and logic:

* The flow's name and description
* Every node, with its name, description, and place on the canvas
* Every connection, including which ones belong to the then branch and which to the else branch
* The condition and the **Expression name** of each **IF/THEN Logic** node
* The attribute names and formulas of each **Initiate Attribute** node

The file leaves out anything that belongs to one workspace or is secret:

* The devices selected in **Data Source** nodes
* Whether the flow is switched on

To create a flow from a file, click **Upload flow** on the start page. Choose the file, or drag it onto the dialog, and click **Upload**. The flow opens on the canvas as a new flow that isn't saved yet. Its name comes from the file name. When a flow with that name already exists, ` (1)` is added to it.

{% hint style="info" %}
**Upload flow** accepts flow files downloaded from any IoT Logic account, not only from this workspace.
{% endhint %}

Each node with settings that the file couldn't store shows **Show warning details**:

| Node | What the warning says |
| --- | --- |
| **Data Source** | Select the devices for this Data Source. |

The mark disappears when you open the node. Fill in what is missing, then save the flow. **Flow enabled** is on for an uploaded flow, so the first save also switches it on and checks the whole flow. See [Why flows won't save](../troubleshooting/flow-wont-save.md).

## In this section

* [Flow editor](flow-editor.md): Learn what the canvas and the node palette are for, and how a flow is assembled.
* [Build conditional flows](conditional-flow.md): Follow a worked example of a flow that branches.
* [Devices without flows](default-processing.md): Learn what reaches the platform when no flow handles a device.
* [Flow templates](templates.md): Start from a prepared flow instead of an empty canvas.
