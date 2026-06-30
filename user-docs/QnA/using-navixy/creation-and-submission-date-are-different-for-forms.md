---
title: Creation and submission date are different for forms
description: Task form creation date precedes submission; check-in form creation date follows submission due to platform database processing logic
---

# Creation and submission date are different for forms

## Question

When we download a forms report from the Submitted Form tab, we notice that for some forms, the submission date and creation date do not match. In fact, there are several instances where the creation date is later than the submission date.

We would expect the creation date to always be earlier than the submission date, so this behavior is unexpected. Why does it happen?

![](<../.gitbook/assets/Unknown image (29)>)

## Answer

This is how the platform logic works for check-in and task forms:

* When a task is created with an assigned form, the platform immediately generates a form for that task. Since the task will require completion at some point, the system prepares a file with an empty form in the database right away - we know the form is needed before it is submitted.
* For check-ins, however, the platform doesn't know in advance that a form should be created. The form is only added to the database after submission, which is why the creation date for check-in forms appears after they're submitted.
