---
description: >-
  Describe what you want to see, and the Dashboard Studio assistant builds the
  dashboard and its queries for you.
tags:
  - guide
  - preview
---

# Build dashboard with AI

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

The AI assistant of Dashboard Studio builds a dashboard from a description in plain language. You describe what you want to monitor, answer a few questions, and check the result on a canvas that runs every panel against your own data. This guide takes you from the first message to a saved dashboard. This guide is for analysts and managers who know what they want to see but don't know which tables or queries they need.


The assistant is part of Dashboard Studio. It is separate from the Navixy AI assistant.


Before you start, you need the following:

* Any role in Dashboard Studio to talk to the assistant and preview a result. To save a dashboard, you need the Admin or Editor role.
* A desktop browser. The chat and the canvas are built for desktop screens.

To build the panels and write their queries yourself, see [Create dashboard](create-dashboard.md).

## 1. Open the assistant

To open the assistant, follow these steps:

1. In the sidebar, click **Home**.
2. On **AI Assistant**, click **Start chatting**.
   The chat opens with the heading "What do you want to monitor?" and four example requests.


When you click an example, its text appears in the message box and isn't sent, so you can change the text first.

## 2. Describe what you want to monitor


Write what you want to see. Include the assets or the group, the period, and the panel types if you know them. A detailed first message leaves the assistant fewer questions to ask.

To send your request, follow these steps:

1. In the message box, enter your request, for example "Distance and driving time per vehicle for each day of the last month".
2. Press Enter, or click **Send**. To start a new line in the message, press Shift+Enter.
   A status line above the message box shows the progress, from "Sending your message…" to "Reply ready", with the elapsed time.

To cancel a request that takes too long, click **Stop**. The chat then shows "You stopped this request, so the assistant did not answer it."


To start from a dashboard that you already have, attach it before you send your request. Click **Base on an existing dashboard**, select the dashboard, and click **Attach**. The assistant reads its panels and their SQL and builds a new dashboard from them. The dashboard that you attached doesn't change.

## 3. Answer the questions

The assistant asks only about what your message didn't say, such as the period or how to group the data. Answer in the chat.


Then the assistant lists the panels that it plans to build and the title of the dashboard. It asks you to confirm. Reply to confirm, or say what to change.

## 4. Check the result on the canvas

The assistant returns a result in the chat. A result with several panels has the heading "Result is ready", the title of the dashboard, and the number of panels. When the canvas is empty, such a result opens on the canvas beside the chat, and the canvas runs the query of every panel against your data. A result with one panel appears in the chat as a panel. To put any result on the canvas, click **New dashboard** on it. Nothing is saved at this point.

The assistant writes each query from your description, without checking your columns first. A query can therefore refer to a column that doesn't exist. Such a query fails on the canvas. Check the status line above the panels:

* "All panels loaded" with the number of panels means that every query ran.
* "Panels loaded" with a number of "Failed panels" means that some queries returned an error. Check them before you save.
* "Panels not checked" means that some panels have an empty query or are in a collapsed row. Check them yourself.

Then check each panel. A panel can load without error and still show the wrong thing, for example when it groups by a different column than you meant.

To change the result, continue the conversation and describe what to change. The assistant returns a new result in the chat, and the dashboard on the canvas doesn't change. Click **New dashboard** on the new result to replace the canvas with it, or **Add to draft** to add its panels to the canvas. **Undo** on the canvas reverses the last change that the canvas received.

These controls on the result help you check it:

* **See SQL** and **See JSON** show the queries and the dashboard definition that the assistant returned, with a button to copy them.
* **Show data** runs the query of a result that has one panel only, without the canvas.
* **Add to draft** adds the panels of another result to the dashboard on the canvas, so you can build one dashboard from several answers.
* **New dashboard** replaces the dashboard on the canvas with this result.

## 5. Save the dashboard


The **Save** button becomes available when the canvas has run every panel. Failed panels don't block saving: you can save and fix the failed queries later in the dashboard editor.

To save the dashboard, follow these steps:

1. To rename the dashboard, click **Rename dashboard** on the canvas and enter the name.
2. Click **Save**.
3. In **Section**, select the section for the dashboard. The first entry, **AI Dashboards**, is the default.
4. Click **Save**.
   A message confirms that the dashboard was saved to the section, with an **Open** button.

The saved dashboard behaves like any other dashboard. You can rename it, change its panels and queries, move it to another section, or export it. It starts with an "Attention" text panel that says that AI generated the dashboard. Delete that panel in the dashboard editor if you don't want it.

After the save, the canvas stays connected to the saved dashboard. Changes that you make on the canvas, and panels that you add with **Add to draft**, update the saved dashboard at the next **Save**. **New dashboard** starts a separate dashboard, so the next **Save** creates a second one.

If **Save** stays unavailable, point to it to read why:

| Message | What to do |
| --- | --- |
| "Available once the canvas has run this dashboard. Check the status above the panels." | Wait until the panels finish running. |
| "A dashboard needs at least one panel" | Ask the assistant to add a panel. |
| "This dashboard is saved. Change something on the canvas to save it again." | Nothing changed since the last save. |
| "Save this dashboard with the Editor role" | Your role can't save. Ask a user with the Admin or Editor role to save it. |

{% hint style="warning" %}
Don't delete or rename the **AI Dashboards** section. If you delete it, the next save can't create the section again and fails. If you rename it, the next save creates a second **AI Dashboards** section, and the renamed section doesn't receive new dashboards.
{% endhint %}

## Start a new conversation


Before you clear the chat, save or export the dashboard on the canvas if you want to keep it. **Export as JSON** on the canvas downloads the dashboard as a file. To start a new conversation, click **Clear chat** and confirm with **Clear**. The assistant clears the conversation and the dashboard on the canvas. Dashboards that you saved don't change.

## Work within the limits of the assistant

The assistant builds dashboards. Start your request with what to build, for example "Build a vehicle mileage dashboard". A message that names only a topic can come back as a short summary of the data without a dashboard, or as "Failed to turn that message into a task. Describe what you need in more detail: what the dashboard should show, for which vehicles and over what period." It doesn't answer one-off questions about your fleet in the chat. A question such as "Which vehicles are offline now" becomes a panel that shows the answer. Every query that the assistant writes only reads data: it can't change, add, or delete anything.

| Limit | Detail |
| --- | --- |
| Message length | A message can have up to 4,000 characters. |
| Message rate | You can send up to 20 messages per minute. After that, the assistant asks you to wait. |
| Conversation length | The chat keeps the newest 100 turns of the conversation. |
| One dashboard on the canvas | The canvas shows one dashboard at a time. A saved dashboard changes only through the canvas that saved it, or in the dashboard editor. |
| Cached results | Panel data on the canvas can be up to 5 minutes old. A query that failed always runs again. |
| Panel count | The result counts every panel. The status line of the canvas counts only panels that run SQL, so a dashboard with a text panel shows one fewer. |
| Chat history | If the chat shows "Chat history isn't being saved for this workspace. It may be lost when the service restarts.", save or export your work before you leave the page. |

## Next steps

* [Create dashboard](create-dashboard.md): Change the panels, queries, and layout of the saved dashboard.
* [Write SQL queries](write-sql-queries.md): Fix a panel query that failed on the canvas.
* [Data schema](../data-access/data-schema/): Learn which tables the queries of the assistant read.
