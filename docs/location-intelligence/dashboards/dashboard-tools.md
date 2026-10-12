---
description: >-
  The tools on every dashboard: search, custom layout, table columns, export,
  feedback, and requests for Dashboard Studio.
tags:
  - reference
  - beta
---

# Search, layout, and export

Every dashboard in [Dashboards](./) has the same tools. You can search it, hide and arrange its panels, choose table columns, export its data, and send feedback. This page describes what each tool does and what it changes.

<figure><img src="../.gitbook/assets/dashboards-conditions.webp" alt="The top of the Conditions, 14 days dashboard. Under the tabs Live status, Trips, 14 days, and Conditions, 14 days, the dashboard title sits above a banner that offers an upgrade to Dashboard Studio. A toolbar holds a search field and the buttons Custom layout, marked with a blue dot, Send feedback, Export PDF, and Update. Each panel header has buttons for export, Dashboard Studio, and help, and tables also have a button for columns."><figcaption>The toolbar and the panel headers of a dashboard</figcaption></figure>

Here's what you can do:

* Find an asset, a value, or a panel, in [Search a dashboard](#search-a-dashboard).
* Hide, move, and resize panels, and hide whole dashboards, in [Custom layout](#custom-layout).
* Choose the columns of a table, in [Table columns](#table-columns).
* Save a dashboard or the data of a panel, in [Export](#export).
* Reload the data, in [Update](#update).
* Report a problem or send a comment, in [Send feedback](#send-feedback).
* Ask for Dashboard Studio, in [Dashboard Studio](#dashboard-studio).

## Search a dashboard

The search field in the toolbar finds text on the current dashboard. It searches panel titles, the panel explanations under **?**, and the values in tables, such as an asset name or a fault code. Navixy highlights every match and shows its position, such as "3/12".

Search highlights matches and dims the panels that have none. It doesn't filter: the values and totals on the panels stay the same. A dashboard has no filters that narrow its panels to some assets, groups, or geo objects.

| Key | Effect |
| --- | --- |
| **Ctrl+K**, or **⌘K** on a Mac | Moves to the search field. |
| **Enter** | Goes to the next match. |
| **Shift+Enter** | Goes to the previous match. |
| **Esc** | Clears the search. |

Search covers only the panels and columns that you can see. When the current dashboard has no match, the message "Matches on other tabs" lists the other dashboards whose panel titles or **?** texts match. The list shows the number of matches on each dashboard. Table values on other dashboards aren't searched until you open them. A dashboard in that list opens when you click it.

## Custom layout

**Custom layout** lets you change how a dashboard looks for you: which panels and dashboards you see, and where the panels are. It doesn't change the data or what other people see. **Custom layout** switches the dashboard to editing, and **Done** ends it.

While you edit the layout, you can:

* **Hide** a panel. **Hidden** in the toolbar lists the hidden panels, so you can show them again.
* Move a panel by dragging its handle and resize it by dragging its corner. After a move, the panels that overlap move down.
* Hide a whole dashboard with the **×** on its tab. **Hidden dashboards** lists them, and **Show all dashboards** shows them all again. You can't hide the last visible dashboard.
* Undo your changes to the panels of the current dashboard with **Reset layout**, after you confirm with **Reset to default**. Hidden dashboards stay hidden.

Your layout is saved in this browser. It isn't available in another browser or on another computer or phone. A blue dot on **Custom layout** shows that the dashboard has a custom layout.

On a screen narrower than 768 pixels, such as a phone, you can hide and show panels, but you can't move or resize them.

When Navixy updates a dashboard, for example with new panels, the layout that you saved for it no longer applies. A message then says "Your saved layout is from an older dashboard version and wasn't applied." **Reset layout** starts again from the new default layout.

## Table columns

**Columns** in the header of a table lets you choose which columns you see. At least one column stays visible, and **Show all columns** shows them all again. Hidden columns are also left out of the search and of the export. The choice is saved in this browser, like the layout.

## Export

You can export a whole dashboard or a single panel:

* **Export PDF** in the toolbar saves the current dashboard as a PDF file with the name of the dashboard. The PDF contains the panels that you see and the date when you exported it. Its tables keep your sorting and leave out the columns that you hid. A long table is cut after about 50 rows, and the PDF says "Partial data shown. Export Excel or CSV to see all rows."
* The export button in the header of a panel saves the data of that panel with **Export to CSV** or **Export to Excel**. The file has the name of the panel and contains the columns that you see, with the units in the column headers.

**Export PDF** becomes available when the dashboard finishes loading.

## Update

Every dashboard reloads its data every 90 seconds. **Update** reloads it immediately. Nothing on the page shows the reload.

A panel that fails to load shows "Failed to load panel data" with the reason. When the dashboards receive too many requests in a short time, the panels show "Too many requests. Please wait a moment and refresh." Wait a moment, then click **Update**.

## Send feedback

**Send feedback** sends your comments on a dashboard to Navixy. Choose the panel that the message is about, or **General (entire dashboard)**, and enter a message of up to 399 characters.

## Dashboard Studio

Dashboard Studio is the premium builder for dashboards. With it, you choose your own metrics, create your own dashboard tabs, add and remove panels, and create and export reports.

**Upgrade to Dashboard Studio** opens a request form for access. The button is in three places: the banner at the top of a dashboard, the footer of a dashboard, and the toolbar while you edit the layout. The Dashboard Studio button in the header of a panel opens the same form. In the form, you can describe the dashboards or metrics that you need. **Get access to Dashboard Studio** sends the request, and the Navixy team contacts you.

You can close the banner with **×**. It appears again when you open Dashboards in a new browser tab or window.

## Phone and tablet

On a screen narrower than 768 pixels, the panels of a dashboard appear in one column, in their order on the dashboard. On a screen from 768 to 1023 pixels wide, they appear in two columns. The tabs of the dashboards scroll sideways on narrow screens.

## See also

* [Live status](live-status.md): Learn what the panels of the Live status dashboard show.
* [Trips, 14 days](trips.md): Learn what the panels of the Trips dashboard show.
* [Conditions, 14 days](conditions.md): Learn what the panels of the Conditions dashboard show.
