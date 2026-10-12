---
description: >-
  How to assign a schedule to an asset and how to build a custom repeat rule.
tags:
  - how-to
  - beta
---

# Manage schedules

{% hint style="info" %}
**Access**

Each task on this page needs a role that allows it. For example, to change a schedule, your role must let you view and edit schedules. See [Access to Business Data Repository](../README.md#access-to-business-data-repository).
{% endhint %}

## Assign a schedule to an asset

An asset stores its schedule in a custom field, and that field is defined in its asset type. If the asset type has no schedule field yet, add one under **Custom fields**, with the field type **Schedule**. Every asset of that type will then have this field, which will be empty until someone fills it. The field has whatever name the asset type gave it, such as Working hours.

To assign the schedule, follow these steps:

1. Open the asset details.
2. In the schedule field, select a schedule from the list. The list shows every schedule in your workspace, and you can search it by name.
3. Click **Save**.

The field now contains your schedule. A schedule marked **Inactive** is switched off and doesn't run, so enable it on the **Schedules** page if the asset needs it.

To swap one schedule for another, select a different one in the field and save again.

### Create a schedule without leaving the asset

Use this workflow when the schedule you need doesn't exist yet.

1. Click <picture><source srcset="../../.gitbook/assets/icons/add-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/add.svg" alt="Create" data-size="line"></picture> in the schedule field.
2. Fill in the panel that opens. It has the same fields as the schedule editor, which [Schedules](README.md#schedule-editor) describes.
3. Click **Save**.

Navixy adds the new schedule to your workspace and selects it in the field. <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> opens whichever schedule is selected in the field, so you can change that schedule the same way.

## Build a custom repeat rule

The preset rules in **Repeat** cover one day of the week. Build your own rule when you need something they can't express: Monday to Friday, every second week, the first Monday of the month, or a rule that stops on a date.

You need a schedule open in the editor, with a time slot that has its **Start** and **End** set. To open an existing schedule, click <picture><source srcset="../../.gitbook/assets/icons/edit-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/edit.svg" alt="Edit" data-size="line"></picture> in its row on the **Schedules** page.

To build the rule, follow these steps:

1. In the **Repeat** list of the time slot, select **Custom**. The rule controls appear under the slot.
2. In **Repeat every**, enter a number and select a unit. The units run from **Second** to **Year**. Set it to 2 and **Week** to repeat the slot every second week.
3. In **End**, choose when the rule stops. **Never** keeps going, **After** stops after a number of times you enter, and **On date** stops on a date you choose.
4. Click **Add recurrence option** and add the conditions that make the rule exact. Each condition appears as a block, and <picture><source srcset="../../.gitbook/assets/icons/close-dark.svg" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/icons/close.svg" alt="Remove recurrence option" data-size="line"></picture> removes it again.
5. To skip a date, such as a public holiday, open **Exception dates** and add it.
6. To add an extra date, such as a one-off weekend shift, open **Additional dates** and add it.
7. Read the **Schedule summary**. It puts the rule into words, which is the quickest way to catch a condition that does something other than what you meant.
8. Click **Save**.

If you switch the slot back from **Custom** to a preset rule or to **Doesn't repeat**, its additional dates and exception dates are removed.

Which conditions you can add depends on the unit in **Repeat every**. **Week numbers**, for example, works only with **Year**. Turning on **All day** removes any **Hours**, **Minutes**, and **Seconds** conditions from the slot. [Schedules](README.md#custom-repeat-rule) lists them all.

{% hint style="info" %}
Your start date may not match the conditions you set. When you save, Navixy can move the start forward to the first matching date. This happens with **Days of the week** on **Week**, **Month**, or **Year**, with **Days of the month** on **Month** or **Year**, and with **Hours**, **Minutes**, or **Seconds** on **Day** or a shorter unit, which can move the start time of day. A slot that starts on a Monday and happens on Saturdays and Sundays first happens on the Saturday after it.
{% endhint %}

### Repeat a slot Monday to Friday

Working hours across the working week are the most common custom rule, and no preset rule can express them.

1. In **Repeat**, select **Custom**.
2. In **Repeat every**, enter 1 and select **Week**. Custom starts at **Day**, so change the unit even if the slot was weekly before.
3. Click **Add recurrence option** and select **Days of the week**.
4. Click **On weekdays**. Monday to Friday are selected together.
5. Click **Save**.

The summary says that the slot happens weekly on weekdays, and gives its start date and when the rule ends.
