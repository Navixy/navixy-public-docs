---
description: >-
  Ask AI Assistant in Navixy Console about your assets, users, billing, or a
  Navixy feature, and have it prepare changes that you approve.
tags:
  - reference
  - beta
---

# AI Assistant in Console

Ask where your assets are, who has access to your workspace, or how to set up a Navixy feature, without leaving [Navixy Console](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/). Navixy AI Assistant in Console answers from the Navixy documentation and from the live data of the organization and workspace that you have open. It can also prepare a change, such as a new asset, and makes the change only after you approve it.

{% hint style="info" %}
**Access**

Anyone who can open Console can use the assistant. The assistant sees only the data that your roles let you see.
{% endhint %}

<figure><img src="../.gitbook/assets/console-hub-ai-assistant-panel.png" alt="The Console hub in Navixy Console with the AI Assistant panel open next to it. The hub lists the products of the workspace, each with Disable and Open buttons or an Enable button. The panel has the assistant's name with a Beta label and buttons to maximize and close the panel. The assistant's first message asks what today's task is. At the bottom of the panel are a message box with dictation and send buttons, a character counter that shows 0/1000, a Privacy policy link, and a note that AI can be wrong."><figcaption><p>AI Assistant in Console, next to the Console hub</p></figcaption></figure>

Here's what you can do:

* Learn which questions the assistant answers, in [Questions that the assistant answers](#questions-that-the-assistant-answers).
* Have the assistant make a change for you, in [Changes that you approve](#changes-that-you-approve).
* Learn which data the assistant reads, in [What the assistant can see](#what-the-assistant-can-see).
* Start a new conversation or go back to an earlier one, in [Conversations](#conversations).
* Check how long a question can be, in [Limits](#limits).
* Dictate a question instead of typing it, in [Dictation](#dictation).

The **AI Assistant** panel opens next to the page when you open Console. To close it, click <picture><source srcset="../.gitbook/assets/icons/close-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/close.svg" alt="Close AI Assistant" data-size="line"></picture> at the top of the panel. To open it again, click <picture><source srcset="../.gitbook/assets/icons/auto_awesome-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/auto_awesome.svg" alt="Open AI Assistant" data-size="line"></picture> in the column of buttons on the right edge of the window. When the panel is open, the same button closes it.

When you close the panel and open it again, it shows the same conversation. After you reload the page, the panel starts a new conversation, and you can open the earlier ones from the menu. To learn how, see [Conversations](#conversations).

To give the panel more room, click <picture><source srcset="../.gitbook/assets/icons/fullscreen-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/fullscreen.svg" alt="Maximize AI Assistant" data-size="line"></picture> at the top of the panel. The panel then takes the place of the page. To make the panel small again, click <picture><source srcset="../.gitbook/assets/icons/fullscreen_exit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/fullscreen_exit.svg" alt="Restore AI Assistant size" data-size="line"></picture>. When the browser window is 1,000 pixels wide or less, the panel always fills the window, and the maximize button is hidden.

The panel uses the theme of Console and changes when you switch the theme. The panel takes the language of Console when it opens for the first time after the page loads. After you change the language of Console, reload the page to change the language of the panel too.

## Questions that the assistant answers

The assistant answers questions about Navixy and about your Navixy account. Enter a question in the message box and press Enter.

You can ask about the following:

* How to use Navixy: features, setup steps, GPS devices, and the Navixy APIs. The assistant answers from the Navixy documentation and adds links to the pages that it used.
* Your assets and devices: where they are and how fast they move
* Your geo objects, trips, and IoT Logic flows
* Your organization and workspace: users and roles, and the billing plan of the workspace

For example, you can ask:

* Where are my assets right now?
* Which users have access to this workspace?
* How do I add a GPS device to Navixy?
* Add a new asset named Van 12.

The assistant answers in text, with tables where they help.

When you aren't sure what to ask, ask the assistant what it can do.

## Changes that you approve

The assistant can prepare a change in your account, such as a new asset. It doesn't make the change until you approve it.

When you ask for a change, for example "Add a new asset named Van 12", the assistant shows the request under **Approve this change?**. The request says what the assistant is going to change, with a short description. Click **Run** to make the change, or **Cancel** to reject it. After **Cancel**, nothing changes.

## What the assistant can see

The assistant works with your own access. It reads and changes only the data that your roles let you see and change. To learn more about roles, see [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles).

The assistant works in the organization and the workspace that you selected in the top bar of Console. When you switch to another organization or workspace, the assistant shows the conversations of that organization and workspace.

## Conversations

The assistant keeps your conversations, and you can go back to them. To see them, open the menu at the top of the panel. The **Chats** list in the menu shows each conversation under its first question. Click a conversation to open it.

You can also do the following:

* To start a new conversation, open the menu and click **New chat**.
* To delete one conversation, point to it in the **Chats** list and click the delete icon next to it.
* To delete all your conversations, click **Clear chat history** in the menu.
* To rate an answer, click the thumbs-up or thumbs-down icon under it. To copy an answer, click the copy icon under it.

To talk to a person at Navixy, click **Contact us** at the bottom of the menu.

## Limits

A question can be up to 1,000 characters long. The counter under the message box shows how many characters you entered. While the assistant answers, the send button in the message box becomes a stop button. Click it to stop the answer.

## Dictation

You can dictate a question instead of typing it. Click the microphone icon in the message box and speak. The text appears in the box, where you can change it before you send it.

## See also

* [Navixy AI Assistant](README.md): Learn which products have the assistant and what it's specialized in.
* [Navixy Console](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/): Learn how to manage your organization, workspaces, and products.
* [Users and roles](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/users-and-roles): Learn how roles decide which data you and the assistant can see.
