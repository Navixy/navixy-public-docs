---
description: >-
  The Schedules page: the list of schedules, the
  schedule editor, and the repeat options that a time slot can take.
tags:
  - reference
  - beta
---

# Schedules

A schedule describes when something happens and how often it repeats. Use one to set the hours a depot is open, the window when a vehicle goes in for service, or the shift a team works.

You build a schedule once and assign it to as many assets and geo objects as you need. You assign it through a **Schedule** [custom field](../assets/#custom-fields) of the asset type or the geo object type.

This page describes the **Schedules** page and the schedule editor. To learn how to work with schedules, see [Set up schedule](set-up-schedule.md) and [Manage schedules](manage-schedules.md).

{% hint style="info" %}
**Access**

Your role decides which schedules you see on this page, and whether you can create, edit, or delete them. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).
{% endhint %}

## Schedules page

**Schedules** is where you view, create, edit, and delete the schedules in your workspace, and turn them on or off. To open it, click **Schedules** on the sidebar.

<figure><img src="../../.gitbook/assets/image (3).png" alt="The Schedules page. A table lists the schedules of the workspace, one row per schedule, with a checkbox and the Name, Description, Pattern, and Status columns. The Status column has a toggle in each row. The table header has a search icon and a Create schedule button. A bar under the table shows how many schedules are selected and the pagination controls."><figcaption><p>The Schedules page with the schedules of a workspace</p></figcaption></figure>

Here's what you can do:

* Add schedules: Click **Create schedule** to open the schedule editor.
* View the schedules in your workspace: The table has one row for each schedule. **Pattern** shows how often the first time slot happens, such as daily or on weekdays. To find a schedule by its name, click <picture><source srcset="../../.gitbook/assets/icons/search-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/search.svg" alt="Search" data-size="line"></picture> above the table and enter part of the name.
* Turn a schedule on or off: Click the **Status** toggle, then confirm with **Enable** or **Disable**. A disabled schedule doesn't run until you enable it again.
* Manage schedules: Open a schedule in the editor with <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> or remove it with <picture><source srcset="../../.gitbook/assets/icons/delete_outline-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete_outline.svg" alt="Delete" data-size="line"></picture>. A deleted schedule can't be restored, so disable it instead if you may need it again.

## Schedule editor

The editor opens in a side panel, titled **Create schedule** when you add one and **Edit schedule** when you open one. A new schedule starts with one time slot that begins now and lasts an hour.

<table><thead><tr><th width="179">Element</th><th width="301">Description</th><th>When you use it</th></tr></thead><tbody><tr><td><strong>Name</strong></td><td>The name of the schedule. Required.</td><td>Always.</td></tr><tr><td><strong>Timezone</strong></td><td>The time zone the schedule runs in, from the standard zone names such as <strong>Europe/Madrid</strong>. Navixy uses the time zone of your browser. Required.</td><td>When your assets work in a different time zone than you do.</td></tr><tr><td><strong>Enable schedule</strong></td><td>A toggle that controls the schedule's status. A schedule that is off doesn't run and shows as <strong>Inactive</strong> when you select a schedule for an asset.</td><td>To pause a schedule without deleting it.</td></tr><tr><td><strong>Schedule summary</strong></td><td>One line for each time slot: how often it happens, when it starts, and when the repetition ends. For a slot that doesn't repeat, the line shows its <strong>End</strong>.</td><td>To check the schedule before you save.</td></tr><tr><td><strong>Time slots</strong></td><td>One entry for each time slot, described in <a href="./#time-slot">Time slot</a>.</td><td>Always. A schedule needs at least one slot.</td></tr><tr><td><strong>Add time slot</strong></td><td>Adds another time slot, so one schedule can cover several periods.</td><td>When the schedule covers a morning shift and an evening shift, or a working day split by a lunch break.</td></tr></tbody></table>

The editor won't save if a required field is empty, a slot that isn't all-day has no **End**, or a slot's **End** isn't later than its **Start**. A message names the slot to fix.

Summer time doesn't move a schedule. A slot that starts at 09:00 still starts at 09:00 after the clocks change.

## Time slot

A time slot is one period of time, for example, Monday from 09:00 to 18:00. The event that occurs during that time can happen once, or regularly, such as every Monday.

<table><thead><tr><th width="183">Element</th><th>Description</th></tr></thead><tbody><tr><td>Slot name</td><td>The field at the top of the slot, without a label. A new slot is called <strong>New time slot</strong> until you rename it.</td></tr><tr><td><picture><source srcset="../../.gitbook/assets/icons/content_copy-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/content_copy.svg" alt="Duplicate time slot" data-size="line"></picture></td><td>Adds another slot with the same settings.</td></tr><tr><td><picture><source srcset="../../.gitbook/assets/icons/delete-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/delete.svg" alt="Delete time slot" data-size="line"></picture></td><td>Removes the slot. Appears only when the schedule has more than one slot, so the last slot can't be removed.</td></tr><tr><td><strong>Start</strong></td><td>For an all-day slot, a date without a time.</td></tr><tr><td><strong>End</strong></td><td>Has to be later than <strong>Start</strong>. An all-day slot can end on the day it starts.</td></tr><tr><td><strong>All day</strong></td><td>Makes the slot cover whole days. <strong>Start</strong> and <strong>End</strong> become dates. An all-day slot covers the same calendar day everywhere, so the schedule's time zone doesn't affect it.</td></tr><tr><td><strong>Repeat</strong></td><td>How often the slot happens, described in <a href="./#repeat-options">Repeat options</a>.</td></tr></tbody></table>

## Repeat options

The **Repeat** list sets how often the event in the time slot happens. It offers five preset rules and the option to create a custom one. The event happens first on its **Start** date, then as often as the rule says: a weekly event that starts on Monday, September 7, happens every Monday after that.

<table><thead><tr><th width="203">Option</th><th>The scheduled event happens</th></tr></thead><tbody><tr><td><strong>Doesn't repeat</strong></td><td>Once, from <strong>Start</strong> to <strong>End</strong>.</td></tr><tr><td><strong>Daily</strong></td><td>Every day at the same time.</td></tr><tr><td><strong>Weekly</strong></td><td>Every week on the same weekday.</td></tr><tr><td><strong>Monthly</strong></td><td>Every month on the same day of the month.</td></tr><tr><td><strong>Yearly</strong></td><td>Every year on the same date.</td></tr><tr><td><strong>Custom</strong></td><td>Based on a rule you build yourself. See <a href="./#custom-repeat-rule">Custom repeat rule</a>.</td></tr></tbody></table>

The preset rules cover one day of the week, not several. For an event scheduled Monday to Friday, or that stops on a specific date, use **Custom**.

## Custom repeat rule

Select **Custom** in **Repeat** to show the rule settings. To learn how to set up such rules, see [Build a custom repeat rule](manage-schedules.md#build-a-custom-repeat-rule).

<table><thead><tr><th width="204">Setting</th><th>Description</th></tr></thead><tbody><tr><td><strong>Repeat every</strong></td><td>A number and a unit: <strong>Second</strong>, <strong>Minute</strong>, <strong>Hour</strong>, <strong>Day</strong>, <strong>Week</strong>, <strong>Month</strong>, or <strong>Year</strong>. Set it to 2 and <strong>Week</strong> to repeat the event in the slot every second week.</td></tr><tr><td><strong>End</strong></td><td>When the rule stops. <strong>Never</strong> keeps going. <strong>After</strong> stops after the number of times you enter. <strong>On date</strong> stops on the date you choose.</td></tr><tr><td><strong>Add recurrence option</strong></td><td>Adds a condition that makes the rule more exact, listed in the next table.</td></tr><tr><td><strong>Additional dates</strong></td><td>Extra dates when the scheduled event happens, such as a one-off weekend shift.</td></tr><tr><td><strong>Exception dates</strong></td><td>Dates when the scheduled event doesn't happen, such as public holidays.</td></tr></tbody></table>

Which conditions you can add depends on the unit in **Repeat every**. **Week numbers**, for example, works only with **Year**. Turning on **All day** removes any **Hours**, **Minutes**, and **Seconds** conditions from the slot.

<details>

<summary>Recurrence conditions</summary>

| Condition             | Accepts                                                              | Quick choices                                                                                       |
| --------------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **Days of the week**  | One or more weekdays.                                                | **On weekdays**, **On weekends**, **All days**, **Clear all**                                       |
| **Months**            | One or more months.                                                  | **All days**, **Clear all**                                                                         |
| **Days of the month** | One or more days, 1 to 31.                                           | **First day**, **Last day**, **Day 15**, **Clear all**                                              |
| **Days of the year**  | Day numbers from 1 to 366, or -1 for the last day of the year.       | None                                                                                                |
| **Week numbers**      | Week numbers from 1 to 53.                                           | None                                                                                                |
| **Hours**             | One or more hours of the day.                                        | **All day**, **Working hours** (the hours 9 to 17, so 09:00 to 17:59), **Morning**, **Evening**, **Night**, **Clear all** |
| **Minutes**           | One or more minutes of the hour.                                     | **Every minute**, **Every 2 minutes**, **Every 5 minutes**, **Every 30 minutes**, **Clear all**     |
| **Seconds**           | One or more seconds of the minute.                                   | **Every 5 seconds**, **Every 10 seconds**, **Every 30 seconds**, **Clear all**                      |
| **Week start day**    | The weekday a week begins on. It matters for rules that count weeks. | None                                                                                                |

</details>

If you switch a slot from **Custom** back to a preset rule or to **Doesn't repeat**, its additional dates and exception dates are removed.

Your start date may not match the conditions you set. When you save, Navixy can move the start forward to the first date that matches. A slot that repeats every week, starts on a Monday, and happens on Saturdays and Sundays first happens on the Saturday after it. With **Repeat every** set to **Day**, **Days of the week** only picks the days and doesn't move the start.

{% hint style="info" %}
A break inside a working day is two slots, not an exception date. An exception date removes a whole slot for that day. Working hours with a lunch break are one slot from 09:00 to 13:00 and one from 14:00 to 18:00.
{% endhint %}

## Schedules assigned to assets

You can assign a schedule to an asset, such as a maintenance period to a vehicle or work days to a person. For this, the asset's type needs a **Schedule** field. On the asset, that field contains the list of all the schedules in your workspace. A schedule that is off shows as **Inactive** on the list.

To learn how to assign schedules, see [Assign a schedule to an asset](manage-schedules.md#assign-a-schedule-to-an-asset).

If you delete a schedule that assets use, the schedule field on those assets shows empty. An asset like this can't be saved again until you clear the field or select another schedule in it.
