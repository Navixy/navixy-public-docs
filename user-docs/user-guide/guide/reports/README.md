---
description: >-
  Create and schedule fleet reports in Navixy. Select from standard report
  types, configure date ranges and object filters, and export to XLSX or PDF.
---

# Reports

Navixy offers a powerful reporting feature that allows you to generate detailed reports on various aspects of your fleet's operations. This guide walks you through the steps of creating, scheduling, and managing reports using the Navixy platform, ensuring you can easily access and analyze the data you need.

<figure><img src="../../.gitbook/assets/Reports module.png" alt="Image of the main page of the Reports module"><figcaption><p>Reports module</p></figcaption></figure>

## How to create a report

A report is a custom-generated document that provides detailed insights into specific aspects of your fleet's operations, such as vehicle activity, fuel consumption, or driver behavior, based on selected criteria and time frames.

<figure><img src="../../.gitbook/assets/Creating a report.png" alt="Image of the report creation dialogue"><figcaption><p>Report creation dialogue</p></figcaption></figure>

Follow these steps to generate a single report:

{% stepper %}
{% step %}
### Choose report type

The **Available reports** panel contains various report types grouped by categories such as **Activity**, **Landmarks**, **Safety and Security**, **Transport usage, Driving quality, Device status, Connected devices,** and **Business**. Click the report you want to generate it.
{% endstep %}

{% step %}
### Select the objects

After choosing the report type, select the objects (vehicles, drivers, etc.) for which the report is generated. Use the **Objects** panel to check the appropriate boxes.
{% endstep %}

{% step %}
### Customize report settings

In the rightmost panel, enter the title for the report and select the date range. You can also choose specific control days and time and decide whether to include summaries or advanced settings, such as dividing by stops, showing stop duration and coordinates, grouping by driver, and using the smart filter. For more information about these options, see [Understanding reports](read-and-understand-reports.md#common-report-parameters).
{% endstep %}

{% step %}
### Build the report

Once all settings are configured, click **Create report**. The report is generated and added to the top of the **Generated reports** list for immediate access.

{% hint style="info" %}
Depending on the number of objects you selected and the actions performed during the period, the report may take some time to generate.
{% endhint %}
{% endstep %}
{% endstepper %}

## How to create scheduled reports

Scheduled reports are automated reports generated at predefined intervals, such as daily, weekly, or monthly, allowing you to regularly receive insights on your fleet's operations without manual intervention. Follow these steps to schedule a report:

{% stepper %}
{% step %}
### Access scheduled reports

In the **Reports** module, navigate to the **Scheduled reports** tab. This section allows you to automate the generation of reports based on a set schedule.
{% endstep %}

{% step %}
### Add a new scheduled report

Click **+** next to the **Scheduled reports** title.
{% endstep %}

{% step %}
### Configure schedule parameters

Select report type and objects and set schedule parameters such as days of week, time of delivery, and interval (e.g., every week, every month). You can also opt to send the reports directly to specified email addresses.
{% endstep %}

{% step %}
### Save the report

After configuring all settings, click **Create**. The scheduled report is now automatically generated according to the specified parameters and can be accessed or modified in the **Scheduled reports** section.
{% endstep %}
{% endstepper %}

## How to manage reports

The following options are available in the report interface after opening it:

* **Download reports:**\
  To download a report, select it from the list and choose <img src="../../.gitbook/assets/image (4).png" alt="Download" data-size="line">. You can save the report in XLSX or PDF.
* **Print reports:**\
  If you need a hard copy of a report, you can print it directly from the browser without downloading it by clicking <img src="../../.gitbook/assets/image (5).png" alt="" data-size="line">.
* **Delete reports:**\
  To remove a report that is no longer needed, select it from the list and click <img src="../../.gitbook/assets/image (8).png" alt="Delete" data-size="line">. Confirm the deletion when prompted.

## See also

* [Report types](report-types.md)
* [How to read and understand reports](read-and-understand-reports.md)
* [Specific report details](specific-report-details/)
