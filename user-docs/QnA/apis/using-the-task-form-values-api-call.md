---
description: Submit and update form values for active tasks via the API. Task must be assigned and form submitted; completed or delayed tasks prevent modifications
---

# Using the Task Form Values API call

**Question: How can the Task Form Values API be used, and under what conditions can form data be modified?** Answer: The Task Form Values API is used to submit and update form data associated with a task in the platform. A key requirement is that the task must first be assigned to a user, and the form must be submitted before any modifications can be attempted. If the form has not been submitted, the API returns a response indicating the problem.

A crucial factor is the task status. Once a task is marked as completed, failed, or delayed, no further modifications can be made, as this restriction preserves the integrity of the submitted data. However, if the task remains in an active state—such as unassigned, assigned, or arrived—the API can still be used to update form values, even after the initial submission. Each successful update triggers a notification in the application indicating that the form has been submitted.

In short, the API works effectively for updates only when the task is active and the form has already been submitted. Otherwise, modifications aren't allowed.

**Links:** [Form/Value/Update API call](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/field-service/task/form/values)
