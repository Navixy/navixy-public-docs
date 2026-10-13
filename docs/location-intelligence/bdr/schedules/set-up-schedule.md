---
description: >-
  How to create a schedule and assign it to an asset.
tags:
  - guide
  - beta
---

# Set up schedule

Follow this guide to create a schedule and assign it to an asset. Use a schedule for the hours a depot is open, the window when a vehicle goes in for service, or the shift a team works.

This guide takes the shortest way through. It builds one schedule that repeats on a preset rule, then assigns it to one asset. Where a step has a longer version, it links to it.

Before you start, you need an asset and its asset type. Learn how to create them in [Create asset](../assets/create-asset.md). You also need a role that lets you create schedules and edit assets. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).

## 1. Create the schedule

To create a schedule, follow these steps:

{% stepper %}
{% step %}

### Open the schedule editor

Click **Schedules** on the sidebar, then click **Create schedule**. You will open a side panel with one time slot in it.
{% endstep %}

{% step %}

### Name the schedule

Enter a **Name**, for example, "Warehouse hours" or "Winter service window." You will use this name to assign the schedule to an asset later.
{% endstep %}

{% step %}

### Choose the time zone

Select a **Timezone**. Summer time doesn't move a schedule. A slot that starts at 09:00 still starts at 09:00 after the clocks change.

Navixy automatically detects and uses the time zone of your browser. You can change it to fit your assets' time zone.
{% endstep %}

{% step %}

### Add a description

Optional. It shows in the **Description** column of the schedules list, so use it for what the name doesn't say.
{% endstep %}

{% step %}

### Leave the schedule enabled

**Enable schedule** is on for a new schedule, and you can leave it that way. Turn it off to save a schedule you aren't ready to run.
{% endstep %}

{% step %}

### Set the time slot

A time slot is one period of time, for example, Monday from 09:00 to 18:00. Set the **Start** and **End** of the slot.

The slot starts out named **New time slot**. Give it a name that says what the period is, such as Morning shift.
{% endstep %}

{% step %}

### Choose how often the slot happens

Open the **Repeat** list of the slot and select a rule:

- **Doesn't repeat** for a one-off period.
- **Daily** for every day at the same time.
- **Weekly** for the same weekday every week.
- **Monthly** for the same day of every month.
- **Yearly** for the same date every year.

These rules cover one day of the week, not several. For Monday to Friday, or a rule that stops on a date, see [Build a custom repeat rule](manage-schedules.md#build-a-custom-repeat-rule).
{% endstep %}

{% step %}

### Check the summary and save

Read the **Schedule summary**. It gives you one line for each slot: how often it happens, when it starts, and when the repetition ends.

Click **Save**.
{% endstep %}
{% endstepper %}

Your schedule now exists and is enabled. It appears on the **Schedules** page, and you can select it on any asset that has a schedule field. Nothing uses it yet.

{% hint style="info" %}
A schedule can have more than one time slot. A working day with a lunch break is one slot from 09:00 to 13:00 and one from 14:00 to 18:00. Click **Add time slot** and fill in the second slot the same way.
{% endhint %}

## 2. Add a schedule field to the asset type

An asset can have a schedule only when its asset type has a field for one. Skip this section if the asset type already has a schedule field.

To add the field, follow these steps:

1. Open the asset type of your asset.
2. Under **Custom fields**, click **Add field**.
3. Select **Schedule** in the list of field types.
4. In the new field's box, labeled **Schedule**, replace the name **Schedule** with a name for the field, for example, Working hours.
5. Click **Save**.

Every asset of the type now shows the field.

## 3. Assign the schedule to the asset

To assign the schedule, follow these steps:

1. Open the asset.
2. In the field you added to the asset type, select your schedule.
3. Click **Save**.

{% hint style="success" %}
The asset now names your schedule in its schedule field, and the schedule is on. Every asset that shares the schedule reads the same hours, so you change them in one place.
{% endhint %}

## Next steps

- [Manage schedules](manage-schedules.md): Assign a schedule to an asset and build a custom repeat rule.
- [Schedules](README.md): Learn how schedules, time slots, and repeat rules work, including every repeat condition.
