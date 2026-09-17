---
title: Building dashboards with AI
description: >-
  Describe what you want to monitor in plain language and let the AI Assistant
  build a SQL-backed dashboard you review on the canvas before saving it
---

# Building dashboards with AI

The AI Assistant builds a Dashboard Studio dashboard from a plain-language description. You describe what you want to monitor. The assistant asks clarifying questions, then generates the panels and the SQL behind them. The result opens on a canvas beside the chat, where it runs against your own data. Nothing is saved until you click **Save**.

This assistant is built into Dashboard Studio. It is separate from the platform-wide Navixy AI assistant, which answers account questions and builds IoT Logic flows.

Use it when you know what you want to see but not which tables or queries produce it. If you prefer to build panels and write queries yourself, see [Creating dashboards](creating-dashboards.md).

{% embed url="https://youtu.be/yFsCA1jGggM?si=WM7QCc30spIlyYR8" %}
Create custom dashboard with a built-in AI Assistant
{% endembed %}

## What the AI Assistant does

The assistant writes the SQL for you. It generates a complete dashboard: the panel layout, the visualization types, and a query behind each data panel. Every query runs against your IoT Query database.

It produces the same visualization types you can add from the panel gallery: single-value tiles, bar charts, pie charts, tables, text panels, and maps. It can also generate types the panel gallery doesn't offer, such as time series and line charts, which render correctly once saved.

Every dashboard is a starting point, not a finished result. The assistant generates SQL from a description without checking your column names first. A panel can therefore reference a column that doesn't exist in your data. The canvas runs each panel so that you catch these errors before you save.

## Before you start

You need the following:

* Active IoT Query access, and Dashboard Studio available in your account. See [How to access Dashboard Studio](./#how-to-access-dashboard-studio).
* An admin or editor role. Anyone can open the assistant and review a result on the canvas, but only admins and editors can save one.
* A full-size browser window. The chat and the canvas are built for desktop widths.

{% hint style="info" %}
In a demo account, the dashboard is saved to your browser rather than to your database. It disappears when the demo data is reset, and nobody else can see it. Demo chat history is never saved.
{% endhint %}

## How to build a dashboard

{% stepper %}
{% step %}

### Open the assistant

Open Dashboard Studio and go to **Home**. Under **Get started**, find the **AI Assistant** card and click **Start chatting**.

On a first visit, the chat opens with the heading **What do you want to monitor?** and four example prompts. Clicking a prompt fills the message box without sending it. Once the conversation has started, the transcript replaces that screen and the example prompts no longer appear.
{% endstep %}

{% step %}

### Describe what you want to monitor

Type your request in plain language, for example "Track vehicle mileage over the last month". Press **Enter** to send it, or **Shift+Enter** for a new line.

Be specific about the metric, the vehicles, the period, and the panel types you want. Stating them at the start shortens the conversation.

While the assistant works, a status line above the composer reports the stage it has reached. It runs from "Sending your message…" through "The assistant is working…" to "Reply ready", and shows the elapsed time beside it.
{% endstep %}

{% step %}

### Answer the clarifying questions

The assistant asks only about what you haven't already stated. A detailed first message can leave a single question, while a short one leaves several. Answer in the chat.

It then lists the panels it plans to build, along with the dashboard title, and asks you to confirm. Reply to confirm, or describe what to change.
{% endstep %}

{% step %}

### Review the result on the canvas

The assistant returns a result card headed **Result is ready**, with the dashboard title and its panel count. The canvas opens beside the chat and runs every panel's SQL against your own data.

No dashboard is created at this stage. Check the status line and the individual panels as described in [What to check on the canvas](#what-to-check-on-the-canvas).
{% endstep %}

{% step %}

### Save the dashboard

Click **Save** on the canvas. In the **Save dashboard** dialog, choose the section the dashboard goes into, then click **Save**.

Dashboard Studio saves the dashboard and shows a confirmation with an **Open** button.
{% endstep %}
{% endstepper %}

If the result isn't what you wanted, continue the conversation. Describe what to change, and the assistant rebuilds the dashboard on the canvas. **Undo** reverts the last change the canvas received.

## Working on the canvas

The canvas is the workspace between the assistant's answer and a saved dashboard. It holds one dashboard at a time. Saving doesn't empty it: the dashboard stays on the canvas, now linked to the saved copy. It changes only when you put another result there or clear the chat.

| Control                     | What it does                                                                                                     |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Save**                    | Opens the **Save dashboard** dialog. Disabled until the canvas has run the dashboard.                            |
| **Undo**                    | Reverts the last change the canvas received from the conversation.                                               |
| Rename                      | Click the pencil icon next to the title to rename the dashboard before you save it.                              |
| **Export as JSON**          | Downloads the dashboard schema, so you can import it elsewhere or keep it outside Dashboard Studio.              |
| Maximize and restore        | Expands the canvas over the full window, then returns it beside the chat.                                        |
| **Hide canvas**             | Collapses the canvas. **Show canvas** in the chat header brings it back.                                         |
| Drag the divider            | Changes how much width goes to the chat and how much to the canvas.                                              |

The result card in the transcript carries its own actions. **New dashboard** puts that result on the canvas on its own, replacing whatever the canvas held. Once a result is the one on the canvas, the same button reads **Open in canvas** and brings the canvas back into view without changing it. **Add to draft** appears while the canvas holds a different result, and merges this result's panels into it, so you can build one dashboard across several answers. **See SQL** and **See JSON** open the generated query and schema, each with a copy button.

A result the assistant returns as a single widget renders in the transcript as a live panel. Click **Show data** to run that panel's query on its own, without opening the canvas.

## What to check on the canvas

The canvas is the only stage that runs the generated SQL against your data, so it is where broken queries surface.

A status line above the panels reports what happened. When every panel works, it reads "All panels loaded: 9." When some fail, it names each number, for example "Panels loaded: 7 of 9. Failed panels: 2. Check them before you save."

A third message means the canvas couldn't validate some panels at all: "Panels not checked: 2. No SQL to run." Those panels were never tested against your data, so check them yourself before saving.

Check each panel individually as well. A panel can load successfully and still show the wrong thing. It might group by a different field than you had in mind, for example.

{% hint style="info" %}
Failed panels don't block **Save**. If only one panel is wrong, you can save the dashboard and fix that panel's query in the dashboard editor afterwards. For help with a failing query, see [Writing SQL queries](writing-sql-queries.md).
{% endhint %}

Panel data on the canvas may be cached for up to five minutes, so values can lag a freshly loaded dashboard. Failed queries are never cached, so a failing panel always runs again.

## Where saved dashboards go

The **Save dashboard** dialog asks which section the dashboard goes into. **AI Dashboards** is always the first entry and the default. The rest of the list is the sections you already have. When you have no **AI Dashboards** section yet, the entry reads **AI Dashboards (new section)**, and Dashboard Studio creates it as it saves, below the sections you made yourself. The dialog has no free-text field, so any other section has to exist already.

Every generated dashboard opens with a full-width **Attention** text panel. It states that the dashboard was AI-generated and that the results may contain inaccuracies. It also carries the job ID and the generation time. The panel comes from the assistant rather than from Dashboard Studio, so its wording can change. It is saved with the dashboard. Remove it in the dashboard editor if you don't want it.

Once saved, the dashboard behaves like any other. You can rename it, edit its panels and queries, move it to a different section, or export it.

### Updating a dashboard you already saved

After a save, the canvas stays connected to that dashboard. Keep talking to the assistant, and **Save** updates the saved copy in place rather than creating a second one. The button's tooltip reads "Update the saved dashboard with your changes", and the confirmation reads that the dashboard was updated.

To create a separate dashboard instead, click **New dashboard** on another result card. That replaces the canvas with a draft that carries no link to the saved copy, so the next **Save** creates a new dashboard.

## Reading the Save button

The **Save** button is disabled more often than it looks, and its tooltip says why.

| Tooltip                                                                      | What to do                                                                       |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| "Available once the canvas has run this dashboard. Check the status above the panels." | Wait for the panels to finish running.                                   |
| "A dashboard needs at least one panel"                                       | Ask the assistant to add a panel.                                                |
| "This dashboard is saved. Change something on the canvas to save it again."   | Nothing has changed since the last save.                                         |
| "Save this dashboard with the Editor role"                                    | Your role can't save. Ask an admin or editor to save it.                         |
| "Update the saved dashboard with your changes"                                | Clicking saves your changes over the copy you saved earlier.                     |

## Starting a new conversation

**Clear chat** in the chat header empties the transcript and starts a new conversation. The assistant doesn't remember what you discussed before, and anything on the canvas is cleared with it. The confirmation dialog states both effects before you commit.

Use it when the assistant has settled on a framing you want it to drop, or when you want to start an unrelated dashboard from scratch. Save or export anything on the canvas first, because clearing discards it.

Clearing doesn't touch dashboards you already saved.

## Known limitations

| Limitation                      | Detail                                                                                                                                                                   |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **One conversation at a time**  | The chat keeps a single conversation. **Clear chat** starts a new one, and the transcript keeps only the newest 100 turns.                                                |
| **Saving requires a role**      | Anyone can open the assistant and review a result. Only admins and editors can save, rename, or edit a dashboard.                                                        |
| **Demo accounts save locally**  | In a demo account, saved dashboards go to your browser, not your database. They disappear when the demo data is reset.                                                   |
| **History isn't always saved**  | In a demo account, and on a workspace where the chat tables aren't set up, the page warns that chat history isn't being saved and may be lost when the service restarts. |
| **Panel counts differ**         | The result card counts every panel. The canvas status line counts only panels that run SQL, so a dashboard with a text panel shows one fewer.                            |
| **Ordering in the section**     | Saved dashboards sort above any dashboard you add to the same section by hand. Reorder them by dragging in the menu editor.                                              |
| **Designed for desktop**        | The chat and the canvas target desktop widths.                                                                                                                          |
| **Message length**              | A single message can be up to 4,000 characters.                                                                                                                         |
| **Message rate**                | Up to 20 messages per minute. Beyond that, the assistant asks you to wait before sending again.                                                                          |

{% hint style="warning" %}
Avoid deleting or renaming the **AI Dashboards** section. Sections are kept rather than erased when you delete one, so a save that tries to recreate it collides with the deleted copy and fails. Restore it in **Edit menu**, then save again. Renaming has the opposite effect: the next save no longer recognizes the section, creates a second **AI Dashboards** beside it, and the renamed one stops receiving new dashboards.
{% endhint %}

## Next steps

* [**Creating dashboards**](creating-dashboards.md): Build a dashboard by hand, add panels, and arrange the layout.
* [**Writing SQL queries**](writing-sql-queries.md): Query patterns for each visualization type, and help with a panel that fails to load.
* [**Built-in dashboards**](built-in-dashboards.md): Ready-made dashboards you can import instead of building one.
