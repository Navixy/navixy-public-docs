---
title: Peak filtering level in fuel volume report
description: Adjust peak filtering level in the Fuel volume report to control how aggressively abnormal sensor spikes are smoothed from the chart
---

# Peak filtering level in fuel volume report

## Question

What does the peak filtering parameter control in the fuel volume report, and how does changing it from Weak to Strong affect the displayed data?

## Answer

The parameter corresponds to the level of peak filtering applied when generating the fuel volume report.

This setting controls how aggressively the system smooths the data by ignoring short or abnormal peaks, which are commonly caused by GPS noise, signal instability, or isolated outlier points.

When the slider is set to Strong, peak filtering is applied more strictly. This results in smoother graphs and aggregated values, as sudden spikes are filtered out.

When set to Weak, the filtering is minimal, and more raw variations are preserved. This is why the displayed data may appear different when switching to a lower value.

Please note that this parameter does not modify the original data stored in the platform. It only affects how the data is processed and visualized in the generated report.

## Links

* [Fuel volume report](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/reports/specific-report-details/fuel-volume-report)
