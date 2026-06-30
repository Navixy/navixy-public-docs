---
description: Interpret Navixy report graphs and tables, apply common parameters like date range and object filters, and export or share generated reports.
---

# How to read reports

Navixy offers a variety of reports designed to help you analyze your fleet’s performance, optimize operations, and make data-driven decisions. This guide helps you understand the structure of these reports, how to interpret their data, and how to customize them to suit your specific needs.

## Common report parameters

When generating a report, several common parameters allow you to tailor the output to your requirements:

* **Report title:** Assign a descriptive name to easily identify the report later.
* **Object selection:** Choose the objects for which you want to generate the report. You can select all objects, specific groups, or individual objects.
* **Date range:** Define the period for the report. Typically, you can select a range of up to three months, though this may vary based on your tariff plan.
* **Control days:** Specify the days of the week you’re interested in, such as excluding weekends if they’re not relevant.
* **Control time:** Focus on specific times of day, such as daytime or nighttime, to filter out irrelevant data.
* **Hide empty tabs:** If a GPS device has no data during the specified period, the corresponding tab isn’t generated.
* **Show seconds:** Displays time in hours:minutes:seconds format for greater precision.
* **Smart filter**: When enabled, the report filters GPS data before calculating trips, mileage, and stops. At the point level, the filter discards readings that form physically impossible sharp-angle jumps (GPS bounces) and low-precision duplicate readings within 30 meters of the previous point, both of which would otherwise add phantom distance to mileage. These checks only apply to consecutive points within 5 minutes of each other. At the track level, the filter also removes micro-tracks shorter than 100 meters or with a bounding diameter under 200 meters. Disable this option if you need the raw, unfiltered GPS data exactly as recorded by the device.

Each report may also include specific parameters tailored to its purpose, which are explained in the documentation for each report type.

## How to read graphs and tables

**Graphs:**

* **Scale control:** Some reports feature graphs with scaling options. You can zoom in on specific data points by using the mouse wheel or dragging the edges of the gray area in the scale graph. This allows for a more detailed view of the data.
* **Graph interaction:** Hovering over the graph displays exact data points, making it easier to interpret trends and patterns.

**Tables:**

* **Sorting and filtering:** You can sort table columns in ascending or descending order by clicking on the column name. This helps in quickly identifying the most or least significant data points.
* **Column visibility:** Clicking the table symbol that appears when hovering over a column allows you to choose which columns are displayed. This customization is useful for focusing on the most relevant data.
* **Color-coded values:** Some tables highlight the largest values in red and the smallest in blue, providing a quick visual cue for identifying key data.

## Operations with reports

**Downloading reports**

Reports can be downloaded in two formats: **XLSX** for flexibility and easy editing, and **PDF** for a static, print-friendly version that closely matches the user interface display. Hidden columns are included in the download, and sorting isn’t applied, making the XLSX format ideal for further data manipulation.

**Deleting reports**

To remove an unnecessary report, click the trash icon. The Navixy platform keeps a maximum of 10 reports visible at a time, automatically deleting the oldest report when a new one is created.

**Printing reports:**

You can print a report by clicking the printer icon, which opens a new window to configure print settings and preview how the report looks on paper.

**Navigating pages:**

Reports are organized into tabs, similar to a web browser. You can easily switch between them to find the specific data you need. If a tab shows "No data found for the selected period," it means the data isn't available for your device.

**Minimizing sections:**

To focus on the most relevant data, you can minimize any section of the report by clicking on the section name. This helps streamline your view and prioritize the information that matters most.
