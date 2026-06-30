---
title: Specifying Teltonika AVL parameters in raw data export
description: "Specify individual Teltonika AVL parameter IDs, comma-separated lists, or hyphen-delimited ranges in the raw data export filter"
---

# Specifying Teltonika AVL parameters in raw data export

## Question

How to specify certain Teltonika AVL parameters when exporting raw data?

## Answer

When exporting a Raw Data spreadsheet for a Teltonika tracker, you can specify either a single AVL parameter, a list of parameters, or a range of AVL parameters in the Raw Data settings window.

Let's break down all possible variations of specifying an AVL in the Raw Data export settings.

First, select an AVL input you need from the list:

![](<../../.gitbook/assets/Unknown image (9)>)

### 1. A range

You can specify a range of numbers for a given AVL ID parameter in the following format:

e.g. `37-453`

![](<../../.gitbook/assets/Unknown image (10)>)

In this case, all AVLs between `avl_io_37` "¦ and "¦ `avl_io_453` will be included in the Raw Data spreadsheet:

![](<../../.gitbook/assets/Unknown image (11)>)

### 2. A set of AVL input numbers

To specify a set of numbers for AVL inputs, use the following format: `37,38,45,599`

![](<../../.gitbook/assets/Unknown image (12)>)

This results in the spreadsheet containing `avl_io_37`, `avl_io_38`, `avl_io_45`, and `avl_io_599` columns:

![](<../../.gitbook/assets/Unknown image (13)>)

### 3. A single number

In this case, just one column for `avl_io_37` will be included in the Raw Data spreadsheet export:

![](<../../.gitbook/assets/Unknown image (14)>)

The same format rules apply to all other numbered parameters and inputs available in the Raw Data export window.

## Links

* [Raw data](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file)
* [Columns in CSV file](https://app.gitbook.com/s/IgDb43gtyXcm1Av4h1np/faq-and-troubleshooting/access-iot-data/save-iot-data-to-csv-file/columns-in-csv-file)
