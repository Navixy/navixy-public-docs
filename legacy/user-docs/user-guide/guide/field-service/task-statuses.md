---
description: >-
  The seven statuses of a Field service task, from Unassigned to Completed with
  errors, and when Navixy sets each one for tasks, route tasks, and checkpoints.
---

# Task statuses

Every task in **Field service** has a status that shows how far the work has progressed. Navixy sets the status automatically. It compares the position of the employee's GPS device with the task location and the execution period. It also checks whether the employee submitted the attached form. This page lists the seven statuses and the conditions that set each one. It is for dispatchers and managers who follow tasks on the **Tasks** page or in reports.

## Statuses and when they apply

The first three statuses describe a task that isn't finished yet. The last four are final: once a task has one of them, Navixy no longer changes its status.

| Status                    | When it applies                                                                                                                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Unassigned**            | No employee is assigned to the task.                                                                                                                                                              |
| **Assigned**              | The task has an employee, and the employee hasn't arrived at the task location yet.                                                                                                              |
| **Arrived**               | The employee is inside the task radius during the execution period, but the task isn't complete yet. The employee still has to stay at the location for the minimum time or submit the form. |
| **Completed**             | The employee met all the completion conditions before the end of the execution period.                                                                                                           |
| **Delayed**               | The employee met all the completion conditions after the end of the execution period, within the admissible delay.                                                                               |
| **Failed**                | The execution period and the admissible delay ended, and the employee didn't meet the completion conditions.                                                                                     |
| **Completed with errors** | Route tasks only. The employee completed every checkpoint, but in a different order from the planned one, or completed the last checkpoint late.                                                |

A single task and each checkpoint of a route task can have any status except **Completed with errors**. A route task as a whole can be **Unassigned**, **Assigned**, **Completed**, **Failed**, or **Completed with errors**.

## Completion conditions

A task is complete when the employee's GPS device is inside the task radius and the employee meets every condition that the task sets:

* **Visit duration**: The employee stayed inside the task radius for at least this time.
* **Ignore random visits with duration less than**: A visit shorter than this time doesn't count as an arrival. The task stays **Assigned** until the employee stays inside the radius for the set time.
* **Form**: If the task has a form, the employee submitted it in the [X-GPS Tracker](../x-gps-mobile-apps/x-gps-tracker/) app.

Navixy counts only the time inside the execution period and the admissible delay. A visit before the execution period starts doesn't count.

When a task has none of these conditions, it skips **Arrived**. The task becomes **Completed** or **Delayed** as soon as the employee enters the task radius.

The execution period, **Visit duration**, **Ignore random visits with duration less than**, and **Admissible delay** are settings of the task. To set them, see [How to create a task](tasks.md#how-to-create-a-task).

## Delayed and failed tasks

The end of the execution period decides between **Completed** and **Delayed**. **Admissible delay** sets how long after that moment the employee can still complete the task.

For example, the execution period of a task ends at 14:00, and its admissible delay is 60 minutes:

* The employee completes the task at 13:50. The status is **Completed**.
* The employee completes the task at 14:30. The status is **Delayed**.
* The employee still hasn't completed the task at 15:00. After 15:00, the status becomes **Failed**.

When **Admissible delay** is 0, a task that isn't complete by the end of the execution period becomes **Failed**.

## Route task statuses

A route task stays **Assigned** while its checkpoints are in progress. It gets its final status when the employee completes the last open checkpoint, or earlier, when a checkpoint fails.

The route task status depends on its checkpoints:

* **Completed**: The employee completed the checkpoints in the planned order, and the last checkpoint is **Completed**.
* **Completed with errors**: The employee completed the checkpoints in a different order from the planned one, or the last checkpoint is **Delayed**.
* **Failed**: One of the checkpoints is **Failed**. The route task becomes **Failed** at the moment that checkpoint fails, even if other checkpoints are still open.

Only the last checkpoint can make the route task **Completed with errors** by being late. When an earlier checkpoint is **Delayed** and the employee completes the last one on time and in the planned order, the route task is **Completed**.

Navixy checks the order by the time when the employee completed each checkpoint. For example, a route task plans Point 1, then Point 2, then Point 3. The employee completes Point 1, then Point 3, and then goes back to Point 2. Every checkpoint is **Completed**, and the route task is **Completed with errors** because the order differs from the plan.

## See also

* [Tasks](tasks.md): Create single and route tasks, and set the execution period and the admissible delay.
* [Forms](forms.md): Attach a form to a task and see how submitting it affects the task status.
* [Task performance](../events-and-notifications/scheduling-and-dispatching/task-performance.md): Get a notification when a task changes status.
