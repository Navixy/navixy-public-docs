---
description: "Every check an IoT Logic flow must pass before it saves, what each message means, and how to fix it."
tags:
  - troubleshooting
  - beta
---

# Why flows won't save

When you save a flow, the flow is checked first. If any check fails, the flow isn't saved, and **The flow wasn't saved. Problems found:** gives the number of problems. Each node with a problem shows **Show error details**: point at it to read what is wrong with that node. A problem that belongs to no single node is listed in the message itself.

Fix every problem reported, then save again. A second save can report a problem that the first save didn't report, most often a clashing attribute name. The same checks run when you switch on a flow.

## The flow is missing a node or a connection

**The flow needs a Data Source node to start from.** Every flow needs somewhere for data to enter. Add a **Data Source** node and connect it to the rest of the flow.

**The nodes aren't connected to each other.** Nodes on the canvas do nothing until connections join them. Draw a connection from each node to the next.

**No message can reach this node.** The marked node has no path leading to it from a **Data Source** node. Connect it, or delete it.

## The connections are wrong

**A Data Source node starts the flow, so no connection can lead into it.** Delete the connection pointing at the **Data Source** node.

**The connections form a loop.** Data moves forward through a flow and never returns to a node that it already passed. Find the connection that leads backwards and remove it.

**A connection leads to a node that isn't in the flow anymore.** A node was deleted while a connection to it remained. Delete the connection.

**Two nodes share one node ID.** Delete one of the two marked nodes and add it again.

**Two live paths reach this node, so one path's work would be lost.** Two branches that are both [live](../how-flows-work.md#live-paths) for the same message meet at one node. Whichever arrives second would overwrite the first. Give each branch its own path to an **Output Endpoint** node.

**A device enters the flow at two Data Source nodes whose paths meet.** The same device is selected in two **Data Source** nodes, and their paths join further on. Remove the device from one of them, or keep the two paths separate.

## An attribute name clashes

**The flow produces the same attribute more than once.** Two **Initiate Attribute** nodes that the same message passes through create an attribute with the same name. Rename one of them. The then and else branches of one **IF/THEN Logic** node can each create the same name, because a message follows only one of them. Two nodes under different **Data Source** nodes with no device in common are allowed. When one device is in two **Data Source** nodes of this flow and both paths produce the name, the message reads "Another flow already produces this attribute", although both nodes are in this flow.

**Another flow already produces this attribute.** A different flow creates an attribute with this name on one or more of the devices this flow also covers. Rename it here, remove it from the other flow, or separate the device lists. Two flows with no devices in common may use the same name.

## The expression can't be evaluated

**The expression can't be evaluated.** The cause is a typo, an unclosed bracket, or a function that doesn't exist. The save checks every expression in the flow, so an expression error is the most common reason that a save fails. Open the node and check the formula against [Expressions](../expressions/).

**This attribute name is reserved by the platform.** The name is one of the 12 reserved position names. These names belong to the device's own position data, and a flow can't produce an attribute with one of these names. Rename the attribute.

**A device already reports an attribute with this name.** The name that you chose is a name that the flow's own devices send. Rename it.

{% hint style="warning" %}
The message **A device already reports an attribute with this name.** can appear on a flow that saved without problems for weeks. The check reads what the devices have actually sent. A device that starts reporting a new name makes a previously valid flow fail its next save.
{% endhint %}

## A device is gone

**A device connected here no longer exists.** A device that you add to a **Data Source** node isn't in this workspace. Remove it from the selection.

The message appears only for a device that you add. A device that was already attached and was deleted later doesn't stop the flow from saving. The same message appears for a device that your role doesn't let you see.

## The save fails without a list of problems

If saving shows an error message and no node shows a problem, your role may not let you change flows. Saving a new flow needs a role that lets you create flows, and saving an existing flow needs a role that lets you edit flows. Ask the people who set up your workspace. See [Access to flows](../flow-management/README.md#access-to-flows).

## Something is too large

| Message | Limit |
| --- | --- |
| The flow holds too many nodes. | 64 nodes |
| The flow holds too many connections. | 96 connections |
| The node produces too many attributes. | 50 attributes in one node |
| The flow produces too many attributes. | 200 attributes in one flow |
| Too many devices are connected to the flow. | 25,000 device connections. A device in two **Data Source** nodes counts twice |
| The expression is too long. | 960 characters for a formula in an **Initiate Attribute** node, 480 for a condition in an **IF/THEN Logic** node |
| The description is too long. | 4,000 characters |

The canvas doesn't let you add a node or a connection beyond the limit. It shows **A flow can hold no more than 64 nodes.** or **A flow can hold no more than 96 connections.** instead.

A flow that reaches the node limit usually does the work of two flows. Two flows for two device groups are simpler to maintain than one flow with 64 nodes.

## The text contains the wrong characters

**A node setting contains characters that aren't Latin. Use only Latin letters.** Use only characters from a standard English keyboard: Latin letters without accents, digits, spaces, and ordinary punctuation. Accented letters, other alphabets, symbols such as °, typographic quotes and dashes, and tabs are refused. Line breaks are refused everywhere except in a description. Replace the character, including in a name or a formula you pasted in.

The rule covers the flow's own name and description as well as the settings inside nodes.

## What the save doesn't check

The save checks that every formula is written correctly. The check uses sample values, not the real data of your devices. A correctly spelled formula is accepted even when it produces nothing on every real message, because no device sends the name that the formula reads.

A flow can therefore save without problems and still do nothing. See [Flow monitoring](../monitoring.md) to find out whether a flow does anything, and [Troubleshooting](./) to find the cause.
