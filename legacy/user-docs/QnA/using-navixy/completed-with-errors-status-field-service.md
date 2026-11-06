---
title: Completed with errors status (Field Service)
description: >-
  A route task gets this status when the employee visits checkpoints out of
  order or completes the last checkpoint late, even if every checkpoint is done.
---

# Completed with errors status (Field Service)

## Question

Task is marked as **Completed with errors**, but all subtasks are complete without errors, so we can't find why the main task is marked as with errors. Can you please help us understand it?

## Answer

Only a route task can be **Completed with errors**. Navixy sets this status when the employee completes the last checkpoint of the route and one of these cases applies:

* The employee completed the checkpoints in the wrong order. Navixy compares the order in which the employee completed the checkpoints with the planned order. For example, the planned sequence was Point 1, then Point 2, and finally Point 3. The employee visited Point 1 first and then Point 3, and only traveled to Point 2 later. Every checkpoint is **Completed**, but the route task is **Completed with errors**.
* The employee completed the last checkpoint late: after its execution period ended, but within the admissible delay. That checkpoint is **Delayed**. A delay at an earlier checkpoint doesn't change the route task status.

The checkpoint statuses don't always show the cause, because the error belongs to the route task as a whole: the order of execution or the time of the last visit.

For the full list of task statuses and the conditions that set each one, see [Task statuses](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/field-service/task-statuses).
