---
description: >-
  How to generate a report in Reports, choose its columns, save it, open a
  saved report, set a default report, and export a report to Excel or PDF.
tags:
  - how-to
  - beta
---

# Manage reports

{% hint style="info" %}
**Access**

The Reports menu lists only the report types that your role lets you see. To generate a report, your role must also let you generate that report type. See [Access to Reports](README.md#access-to-reports).
{% endhint %}

## Generate a report

A report covers the assets and the period that you choose. Choose **Trips & Stops** or **Sensor readings** in the Reports menu. The report settings are under **Scope and period**.

<figure><img src="../.gitbook/assets/reports-scope-and-period.png" alt="The Scope and period settings of a report: the Asset types and Assets fields, the period buttons Today, Yesterday, 7 days, 14 days, and Month, the date range, and the Generate button."><figcaption>The settings under Scope and period choose the assets and the period of a report</figcaption></figure>

To generate a report, follow these steps:

1. Open Reports from the Navixy platform menu.
2. In the Reports menu, select the report type.
3. If the report settings are hidden, click the report name at the top of the report.
4. In **Asset types**, select the types of assets to include.
5. In **Assets**, select the assets. In **Trips & Stops**, every asset that has a GPS device is selected by default.
6. For **Sensor readings** only, select the GPS devices in **Devices**.
7. For **Sensor readings** only, select the values in **Sensors**.
8. Choose a period of up to 31 days. Do one of the following:
   * Click **Today**, **Yesterday**, **7 days**, **14 days**, or **Month**.
   * Click the date range and select the start and end dates and times.

   **7 days** is today and the 6 days before. **14 days** is today and the 13 days before. **Month** is the last 30 days, including today. Reports generates the report when you click a period button or close the date range.

To update the report after you change **Asset types**, **Assets**, **Devices**, or **Sensors**, click **Generate**.

## Choose the columns of a report

You can show and hide the columns of the report screen and change their order. **Customize** keeps this column setup in this browser only. In another browser, the report screen shows the default columns until you open a saved report.

To choose the columns, follow these steps:

1. Click **Customize**.
2. On the **Columns** tab, click a column to show or hide it.
3. Under **Column order**, drag a column to its new place.
4. Click **Save**.

To go back to the default columns, click **Reset to defaults** in **Customize**, then click **Save**.

## Save a report

A saved report stores its assets, its period, and the columns that are visible when you save it. Navixy keeps saved reports on its server, so a saved report opens with the same columns in any browser. Only you can see the reports that you save.

A saved report keeps the period button that you chose, such as **Yesterday** or **7 days**, and calculates its dates again each time that you open it. For example, a report saved with **Yesterday** on September 23 shows September 29 when you open it on September 30. A period that you choose in the date range keeps its dates.

To save a report, follow these steps:

1. Generate the report with the assets, the period, and the columns that you need.
2. Click **Save**.
3. Enter a **Report name**.
4. Click **Save**.

## Open a saved report

A saved report opens in the report type in which you saved it.

To open a saved report, follow these steps:

1. Open the report type in which you saved the report.
2. Click **Open**.
3. Select the saved report.

Reports generates the report again with its saved assets, period, and columns.

## Set a report as the default

The default report opens when you open its report type.

To set a report as the default, follow these steps:

1. Open the saved report.
2. Click **Set as default**.

If you click **Set as default** in a report that you haven't saved, Reports saves it first. The saved report gets the name in **Report name**. When **Report name** is empty, it gets the name of the report type, such as Trips & Stops.

## Export a report

You can export a report as an Excel file or as a PDF.

To export a report, follow these steps:

1. Generate the report.
2. Click **Export**.
3. Select **Excel (XLSX)** or **PDF**.

The Excel file downloads to your computer. It contains the report as it is shown on screen, with every report tab that you opened: **Summary** and the tabs that you added with **+**. The PDF opens in a new browser tab, where you can print it or save it as a file.

You can also export the details of one row as a PDF: the trip details in [Trips & Stops](trips-and-stops.md) or the sensor details in [Sensor readings](sensor-readings.md). To do this, open the details and click **Export PDF**.
