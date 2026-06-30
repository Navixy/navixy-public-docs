---
description: Query audit logs via user/audit/log/list to track output control changes, or create state change alerts and read notification history
---

# Track when output control was changed via API

## Question

Can we get the last updated time of output control through the API?

## Answer

Two options depending on what you need:

1.  **Platform/API change history**

    Use `user/audit/log/list`.

    Filter by output control actions.
2.  **Actual output state change**

    Create an output state change alert.

    Then read notifications via history endpoints.

Summary:

* Use audit logs to track platform/API actions for output control.
* Use events if you want to know when output state physically changed, regardless of how it was triggered.

## Links

* [Audit log list](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/user/audit/audit_log)
* [Output triggering (UI)](https://app.gitbook.com/s/446mKak1zDrGv70ahuYZ/guide/events-and-notifications/inputs-and-outputs/output-triggering)
* [History user list](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/history/history-user#list)
