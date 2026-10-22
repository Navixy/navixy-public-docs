---
description: >-
  The IoT Logic flow editor: the node palette, the canvas, how to connect and
  remove nodes, and what the node settings dialog holds.
tags:
  - reference
  - beta
---

# Flow editor

When you open a flow, it opens on the canvas, where you build the flow. This page describes the editor itself: what is on the screen and how to use it. See the [Nodes](../nodes/) pages to learn what each node type does.

{% hint style="info" %}
**Access**

To save changes in the editor, your role must let you edit flows. The device lists show only the devices that your role lets you see. See [Access to flows](README.md#access-to-flows).
{% endhint %}

## Parts of the flow editor

| Part | What it is |
| --- | --- |
| **Nodes** panel | The node types you can add, in four groups |
| The canvas | The flow itself: nodes and the connections between them |
| **Flow**, in the toolbar | Which flow you are editing, and a way to switch to another flow |
| **Save flow** | Runs every check, then saves the whole flow |

The context menu in the toolbar has **Download** and **Delete** for the flow that you are editing.

An empty flow shows **How to build a flow** and the instruction to drag a node from the **Nodes** panel onto the canvas.

## The Nodes panel

The **Nodes** panel shows the five node types in four groups, in the order that you usually use them.

| Group | Node types | Required |
| --- | --- | --- |
| **Start with** | Data Source | Yes |
| **Process** | Initiate Attribute, IF/THEN Logic | No |
| **Action** | Device Action | No |
| **End with** | Output Endpoint | Yes |

Each entry carries a one-line description of what that node does. When you need more space for the canvas, collapse the panel with the control in its header.

## How to add and connect nodes

{% stepper %}
{% step %}
### Add a node

Drag the node from the **Nodes** panel onto the canvas. The node gets a default name, such as `Data Source 1`. The number in the name counts up for each node of that type.
{% endstep %}

{% step %}
### Connect it

Draw a connection from the earlier node's outgoing point to the later node's incoming point. Direction matters: data moves along the connection, and a flow can never loop back.

An **IF/THEN Logic** node has two outgoing points rather than one. The upper one, marked with a check-mark icon, starts the then branch, which messages that match the condition take. The lower one, marked with a cross icon, starts the else branch, which the other messages take.
{% endstep %}

{% step %}
### Configure it

Open the node to see its settings. See the [Nodes](../nodes/) pages to learn what each field does.
{% endstep %}
{% endstepper %}

A node with a problem shows **Show error details** on the canvas after a failed save. Point at **Show error details** to read the problems with that node.

## The node settings dialog

The settings dialog has the same controls for every node type. The fields of each node type are in the middle of the dialog.

| Control | What it does |
| --- | --- |
| **Name** | What the node is called on the canvas. Up to 255 characters |
| **Apply changes** | Keeps the changes and closes the dialog. Available once you change a field |
| **Cancel** | Discards them |

The dialog of every node type has a help icon next to its title. For **IF/THEN Logic**, **Initiate Attribute**, and **Device Action** nodes, the icon opens a help dialog about that node type. For **Data Source** and **Output Endpoint** nodes, point at the icon to read a short description of the node.

{% hint style="info" %}
**Apply changes** doesn't save the flow. It updates the node on the canvas, and the flow is saved when you click **Save flow**.
{% endhint %}

## How to remove a node or a connection

**A node.** Select it, use **Delete** on the node's toolbar, and confirm with **Delete**. Its connections go with it.

To remove a connection, select it and click **Unlink**. The nodes stay where they are.

## Saving

**Save flow** checks the whole flow and then saves it. The save has one of two results:

* **Flow saved.** The flow is stored as you see it.
* **The flow wasn't saved. Problems found:**, with the number of problems. Each node with a problem shows **Show error details**, and a problem that belongs to no single node is listed in the message.

Nothing is saved when a check fails, so the flow on the server is still the last version that passed. A second save can find problems that the first one didn't report. See [Why flows won't save](../troubleshooting/flow-wont-save.md).

{% hint style="warning" %}
If you leave the editor with unsaved changes, the **Leave without saving?** dialog opens and asks: **Discard unsaved changes or continue editing?** **Discard** loses everything since the last successful save. **Continue editing** returns you to the canvas.
{% endhint %}

## Turning the flow on

The first save of a new flow also switches it on, when **Flow enabled** was left on in the flow form. After that, switch the flow on or off in the flow list, with the switch in the **Status** column. See [Flow management](./).
