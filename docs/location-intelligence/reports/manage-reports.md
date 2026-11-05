---
description: >-
  How to run a report in Reports for the assets and the period that you
  choose, ask for a report in plain language, share it by its link, and
  download its tables as CSV or Excel files.
tags:
  - how-to
  - beta
---

# Manage reports

{% hint style="info" %}
**Access**

The Reports menu lists the report types that your role lets you see, and the report types with a lock. To run a report, your role must also let you run that report type. See [Access to Reports](README.md#access-to-reports).
{% endhint %}

## Open a report

Reports shows one report type at a time. You choose it in the Reports menu.

To open a report, follow these steps:

1. Click **Reports** on the sidebar.
2. In the Reports menu, click **Trips and stops** or **Sensor readings**.

**Trips and stops** opens for **All assets** and **Yesterday** and runs immediately. **Sensor readings** opens for **Yesterday** with no assets and asks you to choose them. Choose the assets, then click **Run**. See [Run a report](#run-a-report).

To make the Reports menu narrow, click **Collapse menu**. The menu then shows only icons. To show the names again, click **Expand menu**.

## Run a report

A report covers the assets and the period that you choose at the top of the report. Two buttons there show the current choice, such as **All assets** and **Yesterday**.

To run a report, follow these steps:

1. Click the button that shows the assets, such as **All assets**.
2. Choose **All assets**, or choose the assets one by one. To find an asset, search by its name or ID.
3. Click the button that shows the period, such as **Yesterday**, then choose the period. See [Choose the period](#choose-the-period).
4. Click **Run**.

You can choose at most 200 assets one by one. To include more than 200 assets, choose **All assets**.

Changes to the assets or the period apply only when you click **Run**. To undo the changes that you haven't run yet, click **Reset**.

The report doesn't update by itself. To run it again with the same assets and period, click **Run**.

In **Sensor readings**, when you change the **Interval**, the report runs again. See [Interval](sensor-readings.md#interval).

## Choose the period

A report covers whole days, from midnight at the start of the first day to midnight at the end of the last day.

<figure><img src="../.gitbook/assets/reports-scope-and-period.png" alt="The period menu of a report, open under the period button. It lists the presets Today, Yesterday, This week, Last week, This month, and Last month. Next to them, a calendar shows two months, with yesterday selected."><figcaption><p>The period menu: choose a preset, or a range of days in the calendar</p></figcaption></figure>

To choose the period, follow these steps:

1. Click the button that shows the period, such as **Yesterday**.
2. Do one of the following:
   * Click a preset.
   * In the calendar, click the first day of the range, then the last day.
3. Click **Run**.

Each preset covers these days:

| Preset | Days |
| --- | --- |
| **Today** | Today |
| **Yesterday** | Yesterday |
| **This week** | The first day of this week to today |
| **Last week** | The whole previous week |
| **This month** | The first day of this month to today |
| **Last month** | The whole previous month |

Your browser's language sets the first day of the week, such as Sunday or Monday. When the language sets no first day, the week starts on Monday.

A range in the calendar is at most 31 days long and ends today at the latest.

A report's dates and times are in your computer's time zone. The report names this time zone, for example "Times are in Europe/Berlin." See [Which time zone a report uses](how-report-data-works.md#which-time-zone-a-report-uses).

## Share a report by its link

A report's page address keeps the assets, the dates, the open tabs, and the filters. It also keeps the interval of **Sensor readings**, and the trip panel, stop panel, or reading panel that is open. A link to the page or a bookmark opens the same report.

To share a report, follow these steps:

1. Run the report with the assets and the period that you need.
2. Copy the page address from the address bar of your browser.
3. Send the address, or save it as a bookmark.

The address keeps the dates, not the preset. For example, if you bookmark a report for **Last week**, the bookmark shows that same week when you open it a month later. To see the most recent week, choose **Last week** again and click **Run**.

When another person opens the link, Reports uses that person's own access. To see the same report, that person needs access to the report type in the same workspace.

## Show and hide columns

Each report table has its own columns menu. Some columns always show: **Asset** in the **By asset** table, **Day** in the **By day** table, and **Sensor** in **Sensor readings**.

To show or hide columns, follow these steps:

1. Click <picture><source srcset="../.gitbook/assets/icons/reports-view_column-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-view_column.svg" alt="Columns" data-size="line"></picture> next to the table.
2. Click a column to show or hide it.

To show every column again, click **Show every column**. While a column is hidden, <picture><source srcset="../.gitbook/assets/icons/reports-view_column-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-view_column.svg" alt="Columns" data-size="line"></picture> has a dot.

Reports keeps your choice in this browser, for your account. The choice applies to the same table on every tab. For example, the **By day** table shows the same columns on the **Days** tab and on the tab of each asset.

## Download a table

You can download each table of a report as a CSV file or an Excel file.

To download a table, follow these steps:

1. Click <picture><source srcset="../.gitbook/assets/icons/reports-download-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-download.svg" alt="Download" data-size="line"></picture> next to the table.
2. Click **CSV** or **Excel (XLSX)**.

In **Sensor readings**, the menu offers two files. **By sensor** is the table itself. **Readings by interval** has one row for each interval of each sensor. Click **CSV** or **Excel (XLSX)** under the file that you need. Hidden columns don't change the **Readings by interval** file.

The file contains the table as it shows on screen. Hidden columns are left out. The sort, the filters, the tab, and the choice of **All**, **Trips**, or **Stops** in the log also apply to the file. The file adds a few columns that the screen doesn't show:

* The file of the **Trip and stop log** starts with **Asset** and **Day**. In place of **Position**, it has **Start latitude**, **Start longitude**, **End latitude**, and **End longitude**.
* On the tab of an asset, the file of the **By day** table starts with **Asset**.

The column headings name the unit and the time zone, for example **Distance (km)** and **Start (Europe/Berlin)**. Durations are in decimal hours: 1 hour 15 minutes is 1.25. Excel shows two decimals, and the CSV file has up to six, such as 2.083333 for 2 hours 5 minutes.

The file name has the table and the first and last day of the period, for example `trips-and-stops-log-2026-10-01-2026-10-07.xlsx`. For a period of one day, the name has one date.

If the file isn't saved, the menu shows "The file could not be written. Try again, or reload the page."

## Ask for a report in plain language

**Ask AI** builds a report from a request in plain language. Describe the report that you need, and Ask proposes the report type, the period, and the assets. Ask works with the report types that you can run.

To ask for a report, follow these steps:

1. Click **Ask AI** at the top right of the page. The **Ask** panel opens next to the report.
2. In the box at the bottom of the panel, describe the report that you need, for example "Trips of all my assets yesterday". In a new conversation, you can also click one of the examples in the panel. The example appears in the box, where you can change it.
3. Press Enter to send the question. To start a new line instead, press Shift+Enter.
4. Wait while the panel shows **Thinking…**. The reply appears under your question.
5. If the reply proposes a report, check the report type, the days, and the assets in the proposal, then click **Open report**.

The report opens for the period and the assets of the proposal and runs. On a narrow screen, the panel closes so that the report shows.

You can name assets by their names and periods in everyday words. "Last week" and the other periods of the period menu cover the same days as in the menu. A proposal follows the rules of the report: at most 31 days and at most 200 assets chosen one by one. Ask can also answer with figures from a report, such as the distance that each asset drove, and tell you which reports you can run.

A question can be up to 2,000 characters long. Ask answers one question at a time, and up to 50 of your questions in a workspace in 24 hours.

### Dictate a question

You can speak your question instead of typing it. To dictate, your browser needs a microphone and permission to use it.

1. Check the language at the bottom left of the box, for example **EN**. To change it, click it and choose a language. The browser remembers your choice.
2. Click <picture><source srcset="../.gitbook/assets/icons/reports-mic-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-mic.svg" alt="Dictate" data-size="line"></picture> and speak. A clock shows how long you have spoken, up to 30 seconds.
3. To finish, click <picture><source srcset="../.gitbook/assets/icons/reports-stop-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-stop.svg" alt="Stop and transcribe" data-size="line"></picture>. After 30 seconds, the recording stops by itself. To cancel the recording, press Esc.

The panel shows **Transcribing…**, and the text appears in the box, after anything that you typed. Check the text, change it if needed, and send the question.

Speak in the language that the box shows. Speech in another language turns into wrong text. You can dictate up to 100 phrases in a workspace in 24 hours. If the text doesn't appear, see [Dictation doesn't fill in the question](troubleshooting.md#dictation-doesnt-fill-in-the-question).

### Continue or start a conversation

Ask keeps your conversation while you move between reports in the same browser tab. Each workspace has its own conversation. Ask keeps a conversation for 30 days after its last question.

* To start a new conversation, click <picture><source srcset="../.gitbook/assets/icons/reports-add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-add.svg" alt="New conversation" data-size="line"></picture> at the top of the panel.
* To close the panel, click **Ask AI** again or press Esc. Your conversation stays when you open the panel again.
* To make the panel wider or narrower, drag its left edge. The browser remembers the width.

If Ask doesn't answer, see [Ask AI doesn't answer](troubleshooting.md#ask-ai-doesnt-answer).

## Report types with a lock

Besides **Trips and stops** and **Sensor readings**, the Reports menu shows other report types with <picture><source srcset="../.gitbook/assets/icons/reports-lock-dark.svg" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/icons/reports-lock.svg" alt="Locked" data-size="line"></picture>. Click one of them to see what it reports.

These report types are offered as paid options. Contact Navixy to add them to your workspace.

<a href="https://navixy.com/en/contact?utm_source=navixy-docs&utm_campaign=reports-paid-options" class="button primary">Contact Navixy</a>
