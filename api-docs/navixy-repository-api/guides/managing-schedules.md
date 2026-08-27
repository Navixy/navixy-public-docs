---
description: >-
  Create, update, query, and delete JSCalendar-aligned schedules compatible with
  RFC 5545 (iCalendar).
---

# Managing schedules

{% include "../.gitbook/includes/navixy-repository-api-is-a-....md" %}

Schedules in Navixy Repository API define time-based rules for your fleet operations, maintenance windows, work hours, restrictions, and more.

The schedule data structure follows [JSCalendar (RFC 8984)](https://www.rfc-editor.org/rfc/rfc8984.html), a JSON format for calendar data, and converts to [RFC 5545](https://www.rfc-editor.org/rfc/rfc5545) (iCalendar) and back without losing information. If you've worked with iCalendar concepts like RRULE, EXDATE, and VTIMEZONE, the model will be familiar: the field names and JSON structure come from JSCalendar, while the repeat rules behave as in iCalendar. If you haven't, that's fine. This guide explains everything you need.

This guide walks you through creating, configuring, updating, and deleting schedules.

## Prerequisites

To work with a schedule, you need your workspace's ID. Use the [me](../actors/#me) query to find it through your membership:

```graphql
query GetMyWorkspace {
  bdr {
    me {
      ... on User {
        memberships {
          nodes {
            workspace {
              id
              title
            }
          }
        }
      }
    }
  }
}
```

You'll receive a response:

```json
{
  "data": {
    "bdr": {
      "me": {
        "memberships": {
          "nodes": [
            {
              "workspace": {
                "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
                "title": "TransLog GmbH"
              }
            }
          ]
        }
      }   
    }
  }
}
```

Use the `id` of the workspace you want to work with for all subsequent schedule operations.

## How schedule data works

A schedule consists of metadata (title and workspace) and calendar data stored in the `scheduleData` field, which accepts a value of [ScheduleData](../schedules.md#scheduledata), a structured JSON object validated on every write.

### Top-level fields

The JSON structure follows the JSCalendar conventions:

<table><thead><tr><th width="115.60003662109375">Field</th><th width="89.20001220703125" data-type="checkbox">Required</th><th>Description</th></tr></thead><tbody><tr><td><code>timeZone</code></td><td>true</td><td>IANA timezone identifier (e.g., <code>Europe/Berlin</code>, <code>America/New_York</code>, <code>UTC</code>). Defines how the API interprets all date-time values in timed events.</td></tr><tr><td><code>events</code></td><td>true</td><td>Non-empty array of time slots, each with a start time, end time or duration, and an optional recurrence rule</td></tr><tr><td><code>active</code></td><td>false</td><td>Boolean. When <code>false</code>, the schedule is disabled without deleting it. Defaults to <code>true</code>.</td></tr><tr><td><code>description</code></td><td>false</td><td>Free-text description. The schedule's display name is the entity <code>title</code>, not this field.</td></tr></tbody></table>

### How timezone works

All date-time values in events (`start`, `end`, `excludedDates`, `additionalDates`, and `recurrenceRule.until`) are local time with no UTC offset or `Z` suffix. The `timeZone` field tells the API how to interpret them.

For example, for `"timeZone": "Europe/Berlin"`:

- `"start": "2025-01-06T06:00:00"` means 6:00 AM Berlin time.
- The API handles daylight saving time (DST) changes automatically, so a recurring event at `06:00:00` stays at 6 AM local time year-round.

All-day events (where `showWithoutTime: true`) use date strings instead of date-times (`2025-06-10`, not `2025-06-10T00:00:00`) and are timezone-independent.

### Event fields

Each event in the `events` array can include:

<table><thead><tr><th width="112.79998779296875">Field</th><th width="112">Required</th><th>Description</th></tr></thead><tbody><tr><td><code>start</code></td><td>Yes</td><td>Start date-time for timed events (<code>2025-01-06T06:00:00</code>) or date for all-day events (<code>2025-01-06</code>). Seconds required. No fractional seconds. No offset or <code>Z</code>.</td></tr><tr><td><code>end</code></td><td>Conditional</td><td>End date-time (or date for all-day). Can't be combined with <code>duration</code>. A timed event needs exactly one of <code>end</code> or <code>duration</code>. An all-day event may omit both.</td></tr><tr><td><code>duration</code></td><td>Conditional</td><td>Duration in ISO 8601 format (e.g., <code>PT9H</code>, <code>PT30M</code>, <code>P1D</code>). Can't be combined with <code>end</code>. Must be positive. For all-day events, must be whole days (e.g., <code>P1D</code>, <code>P2W</code>) with no time component. Preferred over <code>end</code> for recurring events because it stays stable across DST changes.</td></tr><tr><td><code>showWithoutTime</code></td><td>No</td><td>When <code>true</code>, the event is all-day: <code>start</code> and <code>end</code> are dates, and the event ignores <code>timeZone</code>. All-day recurrence rules must not use <code>byHour</code>, <code>byMinute</code>, or <code>bySecond</code>.</td></tr><tr><td><code>recurrenceRule</code></td><td>No</td><td>Recurrence rule defining the repeat pattern. Absent means a single occurrence at <code>start</code>.</td></tr><tr><td><code>excludedDates</code></td><td>No</td><td>Array of local date-times to exclude from recurrence (iCalendar EXDATE). Values must exactly match generated occurrence date-times, including the time component.</td></tr><tr><td><code>additionalDates</code></td><td>No</td><td>Array of local date-times to add as one-off occurrences (iCalendar RDATE).</td></tr><tr><td><code>uid</code></td><td>No</td><td>Stable string identifier for the slot. Useful when tracking individual events across updates.</td></tr><tr><td><code>title</code></td><td>No</td><td>Display label for this event slot (maps to VEVENT SUMMARY).</td></tr></tbody></table>

### Recurrence rule fields

The `recurrenceRule` property supports these fields:

<table><thead><tr><th width="118.4000244140625">Field</th><th width="129">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>frequency</code></td><td>String</td><td><strong>Required.</strong> One of: <code>secondly</code>, <code>minutely</code>, <code>hourly</code>, <code>daily</code>, <code>weekly</code>, <code>monthly</code>, <code>yearly</code>. Lowercase.</td></tr><tr><td><code>interval</code></td><td>Integer</td><td>Repeat every N periods. Must be ≥ 1. Defaults to 1.</td></tr><tr><td><code>count</code></td><td>Integer</td><td>Stop after N occurrences. Can't be combined with <code>until</code>.</td></tr><tr><td><code>until</code></td><td>String</td><td>Stop after this local date-time. Must be ≥ <code>start</code>. Can't be combined with <code>count</code>.</td></tr><tr><td><code>byDay</code></td><td>Object[]</td><td>Days of the week. Each entry is <code>{ "day": "&#x3C;mo|tu|we|th|fr|sa|su>", "nthOfPeriod": &#x3C;integer> }</code>. The optional <code>nthOfPeriod</code> sets an ordinal (e.g., <code>{ "day": "mo", "nthOfPeriod": 1 }</code> = first Monday of the period) and is only valid for <code>monthly</code> or <code>yearly</code> frequency.</td></tr><tr><td><code>byMonth</code></td><td>Integer[]</td><td>Months: 1–12.</td></tr><tr><td><code>byMonthDay</code></td><td>Integer[]</td><td>Days of the month: 1–31, or -31 to -1 from the end (-1 = last day).</td></tr><tr><td><code>byYearDay</code></td><td>Integer[]</td><td>Days of the year: 1–366, or -366 to -1 from the end.</td></tr><tr><td><code>byWeekNo</code></td><td>Integer[]</td><td>Weeks of the year: 1–53, or -53 to -1. Valid only with <code>yearly</code> frequency.</td></tr><tr><td><code>byHour</code></td><td>Integer[]</td><td>Hours: 0–23. Must not be used on all-day events.</td></tr><tr><td><code>byMinute</code></td><td>Integer[]</td><td>Minutes: 0–59. Must not be used on all-day events.</td></tr><tr><td><code>bySecond</code></td><td>Integer[]</td><td>Seconds: 0–60 (60 is valid for leap seconds). Must not be used on all-day events.</td></tr><tr><td><code>firstDayOfWeek</code></td><td>String</td><td>First day of the week for week calculations. One of <code>mo</code>–<code>su</code>. Defaults to <code>mo</code>. (iCalendar WKST.)</td></tr></tbody></table>

### Validation

`scheduleData` is validated on every write against the following rules:&#x20;

- required fields
- pairs that can't be combined (`end`/`duration`, `count`/`until`)
- date-time format (local time only, no offset or `Z`)
- recurrence constraints (`byWeekNo` only with `yearly`, ordinal `byDay` only with `monthly`/`yearly`, no `byHour`/`byMinute`/`bySecond` on all-day events)
- field types

If the input breaks any of these rules, the API rejects the whole write with a [validation error](../error-handling.md#validation-error-400) that names the problem field. The rules apply only to writes. Reading is never validated, so schedules saved before a rule existed can still be read.

## Example scenario: Fleet maintenance schedule

TransLog GmbH needs to schedule weekly maintenance for their vehicle fleet. The maintenance provider works every Monday from 6:00 to 10:00 (Europe/Berlin timezone). Over time, requirements will change: holidays need to be excluded, the contract has an end date, and the maintenance window gets split to accommodate a break.

{% stepper %}
{% step %}
#### Create the schedule

Start with a weekly recurring event. The `scheduleData` field requires a `timeZone` and at least one event with a `start` and either `end` or `duration`.

{% hint style="info" %}
`version` is optional in all mutations. If you leave it out, an update always applies, even when someone else changed the record after you last read it. Include it to catch such conflicts, as the examples in this scenario do. See [Optimistic locking](../optimistic-locking.md) for details.
{% endhint %}

Run this mutation:

```graphql
mutation CreateMaintenanceSchedule {
  bdr {
    scheduleCreate(input: {
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      title: "Weekly fleet maintenance"
      scheduleData: {
        timeZone: "Europe/Berlin"
        events: [
          {
            start: "2025-01-06T06:00:00"
            duration: "PT4H"
            recurrenceRule: {
              frequency: "weekly"
              byDay: [{ day: "mo" }]
            }
          }
        ]
      }
    }) {
      schedule {
        id
        version
        title
      }
    }
  }
}
```

Note the following:

- `timeZone` is `Europe/Berlin`. All date-times in this schedule are Berlin local time: no `Z`, no offset.
- `start: "2025-01-06T06:00:00"` is a Monday, which matches the `byDay: [{ day: "mo" }]` rule. Always make `start` match your repeat rule, otherwise the first occurrence may not land where you expect.
- `duration: "PT4H"` defines a 4-hour window (06:00–10:00). Using `duration` instead of `end` keeps the window stable across DST changes.
- `frequency: "weekly"` with `byDay: [{ day: "mo" }]` means the event repeats every Monday.

The response confirms creation:

```json
{
  "data": {
    "bdr": {
      "scheduleCreate": {
        "schedule": {
          "id": "019a6b2f-793e-807b-8001-555345529b44",
          "version": 1,
          "title": "Weekly fleet maintenance"
        }
      }
    }
  }
}
```

Save the `id` and `version`. You'll need them for updating the schedule.
{% endstep %}

{% step %}
#### Verify the schedule

Query the schedule to confirm it was created correctly:

```graphql
query GetMaintenanceSchedule {
  bdr {
    schedule(id: "019a6b2f-793e-807b-8001-555345529b44") {
      id
      version
      title
      scheduleData
    }
  }
}
```

The `scheduleData` field returns the full JSON structure you provided. Use it to verify the configuration before making further changes.
{% endstep %}

{% step %}
#### Exclude holidays

The maintenance provider doesn't work on public holidays. Several holidays in the year fall on Mondays. Add these as exception dates using `excludedDates`. This requires updating the schedule with [scheduleUpdate](../schedules.md#scheduleupdate).

{% hint style="danger" %}
When updating `scheduleData`, you must provide the complete value, as the API replaces the entire field. Include all existing configuration alongside your changes.
{% endhint %}

An entry in `excludedDates` must exactly match the date and time of the occurrence it removes, not just the date. This event's occurrences start at `06:00:00`, so every excluded date ends with the same `T06:00:00`.

Run this mutation:

```graphql
mutation AddHolidayExceptions {
  bdr {
    scheduleUpdate(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 1
      scheduleData: {
        timeZone: "Europe/Berlin"
        events: [
          {
            start: "2025-01-06T06:00:00"
            duration: "PT4H"
            recurrenceRule: {
              frequency: "weekly"
              byDay: [{ day: "mo" }]
            }
            excludedDates: [
              "2025-04-21T06:00:00",
              "2025-05-05T06:00:00",
              "2025-06-09T06:00:00",
              "2025-10-06T06:00:00"
            ]
          }
        ]
      }
    }) {
      schedule {
        id
        version
        scheduleData
      }
    }
  }
}
```

{% hint style="info" %}
For all-day events (`showWithoutTime: true`), use date-only values in `excludedDates`, for example, `"2025-04-21"` instead of `"2025-04-21T06:00:00"`.
{% endhint %}

The response shows the incremented version:

```json
{
  "data": {
    "bdr": {
      "scheduleUpdate": {
        "schedule": {
          "id": "019a6b2f-793e-807b-8001-555345529b44",
          "version": 2,
          "scheduleData": { "..." : "..." }
        }
      }
    }
  }
}
```
{% endstep %}

{% step %}
#### Set an end date

The maintenance contract runs through December 31, 2025. Add an `until` date to the recurrence rule so the schedule stops repeating after that date.

```graphql
mutation SetContractEndDate {
  bdr {
    scheduleUpdate(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 2
      scheduleData: {
        timeZone: "Europe/Berlin"
        events: [
          {
            start: "2025-01-06T06:00:00"
            duration: "PT4H"
            recurrenceRule: {
              frequency: "weekly"
              byDay: [{ day: "mo" }]
              until: "2025-12-31T23:59:59"
            }
            excludedDates: [
              "2025-04-21T06:00:00",
              "2025-05-05T06:00:00",
              "2025-06-09T06:00:00",
              "2025-10-06T06:00:00"
            ]
          }
        ]
      }
    }) {
      schedule {
        id
        version
      }
    }
  }
}
```

`until` is inclusive: the last occurrence can fall on this date. It must be ≥ `start` and can't be combined with `count`. The schedule's version is now 3.
{% endstep %}

{% step %}
#### Split the schedule into two windows

The maintenance team requests a break from 8:00 to 8:30. Replace the single 4-hour event with two events: 6:00–8:00 and 8:30–10:00.

Each event needs its own `excludedDates` array with times matching that event's `start`. Run this mutation:

```graphql
mutation SplitMaintenanceWindow {
  bdr {
    scheduleUpdate(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 3
      scheduleData: {
        timeZone: "Europe/Berlin"
        events: [
          {
            start: "2025-01-06T06:00:00"
            duration: "PT2H"
            recurrenceRule: {
              frequency: "weekly"
              byDay: [{ day: "mo" }]
              until: "2025-12-31T23:59:59"
            }
            excludedDates: [
              "2025-04-21T06:00:00",
              "2025-05-05T06:00:00",
              "2025-06-09T06:00:00",
              "2025-10-06T06:00:00"
            ]
          },
          {
            start: "2025-01-06T08:30:00"
            duration: "PT1H30M"
            recurrenceRule: {
              frequency: "weekly"
              byDay: [{ day: "mo" }]
              until: "2025-12-31T23:59:59"
            }
            excludedDates: [
              "2025-04-21T08:30:00",
              "2025-05-05T08:30:00",
              "2025-06-09T08:30:00",
              "2025-10-06T08:30:00"
            ]
          }
        ]
      }
    }) {
      schedule {
        id
        version
      }
    }
  }
}
```

The schedule's version is now 4.
{% endstep %}

{% step %}
#### Delete the schedule

When the contract ends and you no longer need the schedule, run this mutation to delete it:

```graphql
mutation DeleteMaintenanceSchedule {
  bdr {
    scheduleDelete(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 4
    }) {
      deletedId
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "scheduleDelete": {
        "deletedId": "019a6b2f-793e-807b-8001-555345529b44"
      }
    }
  }
}
```

Including `version` ensures you don't accidentally delete a schedule that someone else has modified. If the version doesn't match, you'll receive a [conflict error](../error-handling.md#version-conflict-409). See [Optimistic locking](../optimistic-locking.md) for details on when to omit it.
{% endstep %}
{% endstepper %}

## Listing schedules

To retrieve all schedules for a workspace:

```graphql
query ListSchedules {
  bdr {
    schedules(
      workspaceId: "7c9e6679-7425-40de-944b-e07fc1f90ae7"
      first: 20
    ) {
      nodes {
        id
        title
        scheduleData
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

For details on pagination, see [Pagination](../pagination.md).

## Handling version conflicts

If you include `version` in your mutation and the entity has been modified since you last fetched it, the API returns a [conflict error](../error-handling.md#version-conflict-409):

```json
{
  "errors": [
    {
      "message": "Entity has been modified by another request",
      "extensions": {
        "code": "CONFLICT",
        "status": 409,
        "expectedVersion": 3,
        "currentVersion": 4
      }
    }
  ]
}
```

To resolve this:

1. Query the schedule to get the current version and data
2. Merge your changes with the current state
3. Retry the mutation with the correct version

For more details on version conflicts, see [Optimistic locking](../optimistic-locking.md).

## Common patterns

The snippets below show the `recurrenceRule` object in isolation. In practice, each belongs inside an event in the `events` array alongside `start` and `end` or `duration`.

#### Single-parameter patterns

**Every weekday (standard work hours):**

```json
{
  "frequency": "weekly",
  "byDay": [
    { "day": "mo" }, { "day": "tu" }, { "day": "we" },
    { "day": "th" }, { "day": "fr" }
  ]
}
```

**Every other week on Monday (bi-weekly team meetings):**

```json
{
  "frequency": "weekly",
  "interval": 2,
  "byDay": [{ "day": "mo" }]
}
```

**First and fifteenth of each month (payroll processing):**

```json
{
  "frequency": "monthly",
  "byMonthDay": [1, 15]
}
```

**Last day of each month (monthly reports deadline):**

```json
{
  "frequency": "monthly",
  "byMonthDay": [-1]
}
```

**First Monday of each month (monthly fleet review):**

```json
{
  "frequency": "monthly",
  "byDay": [{ "day": "mo", "nthOfPeriod": 1 }]
}
```

#### Multi-parameter patterns

**Every hour during weekday business hours (hourly check-ins):**

```json
{
  "frequency": "hourly",
  "byDay": [
    { "day": "mo" }, { "day": "tu" }, { "day": "we" },
    { "day": "th" }, { "day": "fr" }
  ],
  "byHour": [8, 9, 10, 11, 12, 13, 14, 15, 16, 17]
}
```

{% hint style="info" %}
`byHour`, `byMinute`, and `bySecond` work with any `frequency`. Their only restriction is that an all-day event (`showWithoutTime: true`) must not use them.
{% endhint %}

**Every Monday in January, April, July, and October (quarterly inspections):**

```json
{
  "frequency": "yearly",
  "byMonth": [1, 4, 7, 10],
  "byDay": [{ "day": "mo" }]
}
```

## Complete examples

### Warehouse work hours

Standard weekday schedule with a lunch break, excluding company holidays:

```json
{
  "timeZone": "Europe/Berlin",
  "events": [
    {
      "start": "2025-01-06T08:00:00",
      "duration": "PT4H",
      "recurrenceRule": {
        "frequency": "weekly",
        "byDay": [
          { "day": "mo" }, { "day": "tu" }, { "day": "we" },
          { "day": "th" }, { "day": "fr" }
        ]
      },
      "excludedDates": [
        "2025-01-01T08:00:00",
        "2025-12-25T08:00:00",
        "2025-12-26T08:00:00"
      ]
    },
    {
      "start": "2025-01-06T13:00:00",
      "duration": "PT4H",
      "recurrenceRule": {
        "frequency": "weekly",
        "byDay": [
          { "day": "mo" }, { "day": "tu" }, { "day": "we" },
          { "day": "th" }, { "day": "fr" }
        ]
      },
      "excludedDates": [
        "2025-01-01T13:00:00",
        "2025-12-25T13:00:00",
        "2025-12-26T13:00:00"
      ]
    }
  ]
}
```

### Refrigerated truck temperature monitoring

Different temperature thresholds for day and night operation:

```json
{
  "timeZone": "Europe/Moscow",
  "events": [
    {
      "start": "2025-01-06T06:00:00",
      "duration": "PT16H",
      "recurrenceRule": { "frequency": "daily" }
    },
    {
      "start": "2025-01-06T22:00:00",
      "duration": "PT8H",
      "recurrenceRule": { "frequency": "daily" }
    }
  ]
}
```

### Equipment rental periods

Non-recurring schedule for specific rental dates:

```json
{
  "timeZone": "America/New_York",
  "events": [
    {
      "start": "2025-02-10",
      "end": "2025-02-15",
      "showWithoutTime": true
    },
    {
      "start": "2025-03-01",
      "end": "2025-03-10",
      "showWithoutTime": true
    }
  ]
}
```

## Attaching schedules to other entities

A schedule on its own is just calendar data. To put it to work, link it to the entity it governs, such as the asset it defines maintenance windows for. The link is a `REFERENCE` [custom field](implementing-custom-fields.md) on the asset type, with `refEntityTypeCode: "schedule"` fixing what the field may point at. The same pattern links entities to tags and catalog items — only the `refEntityTypeCode` changes.

{% stepper %}
{% step %}
### Define the reference field

Add the field definition to the asset type. See [Implementing custom fields](implementing-custom-fields.md) for the full definition workflow:

```graphql
mutation AddMaintenanceScheduleField {
  bdr {
    assetTypeUpdate(input: {
      id: "b1ffcd00-0d1c-5f29-cc7e-7cc0ce491b22"
      version: 2
      customFieldDefinitions: [
        {
          create: {
            code: "cf_maintenance_schedule"
            title: "Maintenance Schedule"
            fieldType: REFERENCE
            params: { reference: { refEntityTypeCode: "schedule", isRequired: false } }
          }
        }
      ]
    }) {
      assetType {
        id
        version
      }
    }
  }
}
```

The response returns the type with its incremented `version`, as in the earlier update examples.
{% endstep %}

{% step %}
### Attach the schedule to an asset

Set the field on the asset, using the schedule's ID as the value. `isPrimary` is required on every reference value, so state it explicitly:

```graphql
mutation AttachScheduleToTruck {
  bdr {
    assetUpdate(input: {
      id: "019a6b2f-793e-807b-8001-555345529b44"
      version: 2
      customFields: {
        set: [
          {
            code: "cf_maintenance_schedule"
            value: { reference: { id: "019a7c3e-88a1-807b-8001-777567741c66", isPrimary: false } }
          }
        ]
      }
    }) {
      asset {
        id
        version
      }
    }
  }
}
```

The response confirms the update with the asset's new `version`.
{% endstep %}

{% step %}
### Read the schedule back from the asset

Select the field and use inline fragments, first on the value type, then on `Schedule`:

```graphql
query GetTruckSchedule {
  bdr {
    asset(id: "019a6b2f-793e-807b-8001-555345529b44") {
      title
      customFields(codes: ["cf_maintenance_schedule"]) {
        code
        ... on ReferenceCustomFieldValue {
          refs {
            ... on Schedule {
              id
              title
              scheduleData
            }
          }
        }
      }
    }
  }
}
```

Response:

```json
{
  "data": {
    "bdr": {
      "asset": {
        "title": "Truck B-44 (Hamburg–Berlin)",
        "customFields": [
          {
            "code": "cf_maintenance_schedule",
            "refs": [
              {
                "id": "019a7c3e-88a1-807b-8001-777567741c66",
                "title": "Weekly maintenance",
                "scheduleData": {
                  "timeZone": "Europe/Berlin",
                  "events": [
                    {
                      "start": "2025-01-06T06:00:00",
                      "duration": "PT4H",
                      "recurrenceRule": { "frequency": "weekly" }
                    }
                  ]
                }
              }
            ]
          }
        ]
      }
    }
  }
}
```

`refs` is always a list, and a single-value field returns one element. An element is `null` when the referenced schedule was deleted after being linked, so handle that case when reading.
{% endstep %}
{% endstepper %}

To detach the schedule, remove the field with `unset: ["cf_maintenance_schedule"]` in `assetUpdate`, the same way device fields are unlinked in [Working with assets](working-with-assets.md#linking-devices).

## See also

* [Schedules](../schedules.md): Complete reference for all schedule operations and types
* [Implementing custom fields](implementing-custom-fields.md): Define custom fields and store your own data on entities
* [Working with assets](working-with-assets.md): Create and manage assets such as vehicles and equipment
