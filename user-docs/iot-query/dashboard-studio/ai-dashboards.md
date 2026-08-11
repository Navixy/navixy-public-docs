---
title: Building dashboards with AI
description: Describe what you want to monitor in plain language and let the AI Assistant build a SQL-backed dashboard you preview against your own data before saving it
---

# Building dashboards with AI

The AI Assistant builds a Dashboard Studio dashboard from a plain-language description. You describe what you want to monitor. The assistant asks clarifying questions, then generates the panels and the SQL behind them. You preview the result against your own data before saving it.

This assistant is built into Dashboard Studio. It is separate from the platform-wide Navixy AI Assistant, which answers account questions and builds IoT Logic flows.

Use it when you know what you want to see but not which tables or queries produce it. If you prefer to build panels and write queries yourself, see [Creating dashboards](creating-dashboards.md).

## What the AI Assistant does

The assistant writes the SQL for you. It generates a complete dashboard: the panel layout, the visualization types, and a query behind each data panel. Every query runs against your IoT Query database.

It produces the same visualization types you can add from the panel gallery: single-value tiles, bar charts, pie charts, tables, text panels, and maps. It can also generate types the panel gallery doesn't offer, such as time series and line charts, which render correctly once saved.

Two boundaries shape how you use it:

* **The assistant creates new dashboards. It can't modify an existing one.** To change a dashboard after you save it, edit it in the dashboard editor as usual.
* **Every dashboard is a starting point, not a finished result.** The assistant generates SQL from a description without checking your column names first. A panel can therefore reference a column that doesn't exist in your data. Previewing lets you catch such errors before you save.

## Before you start

You need the following:

* Active IoT Query access, and Dashboard Studio available in your account. See [How to access Dashboard Studio](./#how-to-access-dashboard-studio).
* An admin or editor role. Anyone can open the assistant and preview a result, but only admins and editors can save one.
* A full-size browser window. The chat and the preview grid are built for desktop widths.

{% hint style="info" %}
In a demo account, **Apply** saves the dashboard to your browser rather than to your database. The dashboard disappears when the demo data is reset, and nobody else can see it. Demo chat history is never saved.
{% endhint %}

## How to build a dashboard

{% stepper %}
{% step %}
**Open the assistant**

Open Dashboard Studio and go to **Home**. Under **Get started**, find the **AI Assistant** card and click **Start chatting**.

On a first visit, the chat opens with the heading **What do you want to monitor?** and four example prompts. Clicking a prompt fills the message box without sending it. Once the conversation has started, the transcript replaces that screen and the example prompts no longer appear.
{% endstep %}

{% step %}
**Describe what you want to monitor**

Type your request in plain language, for example "Track vehicle mileage over the last month".

Be specific about the metric, the vehicles, the period, and the panel types you want. Stating them at the start shortens the conversation.
{% endstep %}

{% step %}
**Answer the clarifying questions**

The assistant asks only about what you haven't already stated. A detailed first message can leave a single question, while a short one leaves several. Answer in the chat.

It then lists the panels it plans to build, along with the dashboard title, and asks you to confirm. Reply to confirm, or describe what to change.
{% endstep %}

{% step %}
**Preview the result**

The assistant returns a result card with the dashboard title, its panel count, and a **Preview** button. Click **Preview**.

The preview runs every panel's SQL against your own data. No dashboard is created at this stage. Once the preview finishes, **Apply** becomes available.
{% endstep %}

{% step %}
**Apply the dashboard**

Click **Apply**. Dashboard Studio saves the dashboard into a section named **AI Dashboards** and opens it.
{% endstep %}
{% endstepper %}

If the result isn't what you wanted, close the preview and continue the conversation. Describe what to change, and the assistant rebuilds the dashboard. Preview the new result before applying it.

## What to check in the preview

The preview is the only stage that runs the generated SQL against your data, so it is where broken queries surface.

A status line above the panels reports what happened. When every panel works, it reads **All 9 panels loaded.** When some fail, it names both numbers, for example **7 of 9 panels loaded. 2 panels failed — check them before applying.**

A third message means the preview couldn't validate some panels at all: **2 panels could not be checked — they have no SQL to run.** Those panels were never tested against your data, so check them yourself before applying.

Check each panel individually as well. A panel can load successfully and still show the wrong thing. It might group by a different field than you had in mind, for example.

{% hint style="info" %}
Failed panels don't block **Apply**. If only one panel is wrong, you can apply the dashboard and fix that panel's query in the dashboard editor afterwards. For help with a failing query, see [Writing SQL queries](writing-sql-queries.md).
{% endhint %}

Two differences between the preview and the saved dashboard are worth knowing. The preview expands collapsed rows so it can run every panel, and it doesn't refresh on its own. Panel data may also be cached for up to five minutes, so values can lag a freshly loaded dashboard. Failed queries are never cached, so a failing panel always runs again.

## Where applied dashboards go

Applied dashboards are saved into a section named **AI Dashboards** in the Dashboard Studio sidebar. Dashboard Studio creates this section the first time you apply a dashboard, at the bottom of the sidebar, and reuses it after that.

Every generated dashboard opens with a full-width **Attention** text panel. It states that the dashboard was AI-generated and that the results may contain inaccuracies. It also carries the job ID and the generation time. The panel comes from the assistant rather than from Dashboard Studio, so its wording can change. It is saved with the dashboard. Remove it in the dashboard editor if you don't want it.

Once saved, the dashboard behaves like any other. You can rename it, edit its panels and queries, move it to a different section, or export it.

{% hint style="info" %}
Applying the same result twice creates two separate dashboards rather than updating the first. To revise a dashboard you already applied, edit the saved copy or delete it before applying a new version.
{% endhint %}

## Known limitations

| Limitation | Detail |
| --- | --- |
| **New dashboards only** | The assistant can't edit a dashboard you already have. Edit saved dashboards in the dashboard editor. |
| **Saving requires a role** | Anyone can preview. Only admins and editors can apply, rename, or edit a dashboard. |
| **Demo accounts save locally** | In a demo account, applied dashboards go to your browser, not your database. They disappear when the demo data is reset. |
| **One continuous conversation** | The chat has no control for starting a new conversation, and the transcript keeps only the newest 100 turns. |
| **History isn't always saved** | In a demo account, and on a workspace where the chat tables aren't set up, the page warns that chat history isn't being saved and may be lost when the service restarts. |
| **Panel counts differ** | The result card counts every panel. The preview status line counts only panels that run SQL, so a dashboard with a text panel shows one fewer. |
| **Ordering in the section** | Applied dashboards sort above any dashboard you add to **AI Dashboards** by hand. Reorder them by dragging in the menu editor. |
| **Designed for desktop** | The chat and the preview grid target desktop widths. |
| **Message length** | A single message can be up to 4,000 characters. |
| **Message rate** | Up to 20 messages per minute. Beyond that, the assistant asks you to wait before sending again. |

{% hint style="warning" %}
Avoid deleting or renaming the **AI Dashboards** section. If you delete it, the next **Apply** fails with an error asking you to restore it from the menu editor. If you rename it, the next **Apply** creates a second **AI Dashboards** section beside it, and the renamed one stops receiving new dashboards.
{% endhint %}

## Next steps

* [**Creating dashboards**](creating-dashboards.md): Build a dashboard by hand, add panels, and arrange the layout.
* [**Writing SQL queries**](writing-sql-queries.md): Query patterns for each visualization type, and help with a panel that fails to load.
* [**Built-in dashboards**](built-in-dashboards.md): Ready-made dashboards you can import instead of building one.
