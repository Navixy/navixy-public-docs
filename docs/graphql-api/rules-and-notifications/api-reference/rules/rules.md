---
tags:
  - reference
  - beta
---

# Rules

The rules of a workspace: the conditions set for its assets, with their equations and thresholds, the queries that read rules and list the parameters an equation can name, and the mutations that create, update, and delete rules.

## Queries

### rule

Reads one rule. Null when no live rule of the caller's workspace has that id.

```graphql
rule(id: ID!): RulesRule
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `id` | `ID!` | The rule to read. |

**Output types:**

<details>

<summary>RulesRule</summary>

A rule a workspace wrote over the telemetry of its assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Titled](../../../core-api-reference/common.md#titled), [Versioned](../../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `notificationText` | `String` | The text a notification carries when the rule fires. Absent when the rule sends its title. |
| `coversAllAssets` | `Boolean!` | Whether the rule covers every asset of the workspace, the ones that exist now and the ones added later. |
| `assetIds` | `[ID!]!` | The covered assets the caller may read, in id order; empty when the rule covers all. Assets outside the caller's `bdr.asset` read access are left out and stay attached. |
| `assetCount` | `Int!` | The count of `assetIds`. |
| `equations` | [[RulesEquation](#rulesequation)!]! | The chain, in evaluation order. The first equation carries no connector. |
| `isEnabled` | `Boolean!` | Whether the engine evaluates this rule. A rule is created disabled. |
| `createdAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |

</details>

---

### rules

Lists the rules of the caller's workspace.

```graphql
rules(
    filter: RulesRuleFilter
    first: Int
    after: String
    last: Int
    before: String
    orderBy: RulesRuleOrder = { field: TITLE, direction: ASC }
  ): RulesRuleConnection!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `filter` | `RulesRuleFilter` | Filtering options for the returned rules. |
| `first` | `Int` | The first `n` elements from the [paginated list](../../../pagination.md). |
| `after` | `String` | The elements that come after the specified [cursor](../../../pagination.md). |
| `last` | `Int` | The last `n` elements from the [paginated list](../../../pagination.md). |
| `before` | `String` | The elements that come before the specified [cursor](../../../pagination.md). |
| `orderBy` | `RulesRuleOrder` | The ordering options for the returned rules. |

**Input types:**

<details>

<summary>RulesRuleFilter</summary>

Filtering options for rules. All criteria are ANDed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `isEnabled` | `Boolean` | Only enabled, or only disabled, rules. |
| `assetId` | `ID` | Only rules that apply to this asset: those naming it and those covering all. None for an asset the caller may not read. |

</details>

<details>

<summary>RulesRuleOrder</summary>

Ordering options for rules.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [RulesRuleOrderField](#rulesruleorderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

</details>

**Output types:**

<details>

<summary>RulesRuleConnection</summary>

A paginated list of RulesRule items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RulesRuleEdge](#rulesruleedge)!]! | A list of edges. |
| `nodes` | [[RulesRule](#rulesrule)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

</details>

---

### parameterFields

The fields a parameter equation may name for the assets in `assetIds`, or for every asset the caller may read when
  `assetIds` is omitted or empty: the comparable fields tracking holds for their devices, plus `speed`, `altitude` and
  `heading` when they have a device, whether or not a position ever arrived. Sorted by field, index, type and unit; a
  field reported with two types or units appears once for each. An id the caller may not read, or of another
  workspace, contributes nothing. The list holds what the devices have reported, not every field they can report, and
  carries no titles, which belong to the field dictionary of the telemetry contract; SERVICE_UNAVAILABLE when
  tracking's readings are off or cannot be read.

```graphql
parameterFields(
    assetIds: [ID!]
  ): [RulesParameterField!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `assetIds` | `[ID!]` | The assets the rule covers, at most as many as a rule may name. Omitted or empty: every asset the caller may read. |

**Output types:**

<details>

<summary>RulesParameterField</summary>

A field a parameter equation may name, as the devices of the assets report it. `field` and `index` are what
`RulesEquationInput` takes; `valueType` says which member of `RulesValueInput` the threshold sets.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | `String!` | The field's name as the telemetry carries it, without the channel suffix. |
| `index` | `Int` | The channel, for a field that has one (`lls_level` with index 2 is `lls_level_2`). Absent otherwise. |
| `valueType` | [RulesValueType](#rulesvaluetype)! | The type of the field's values; BOOLEAN and STRING take EQ and NE only. |
| `unit` | `String` | The unit the devices label the field with, or the telemetry contract's unit of `board_voltage`, `battery_voltage`, `battery_level` and `hw_mileage` when they send none. Absent for a field without one, and for every type but NUMBER. A threshold may name it only when it is one of the codes `RulesThresholdInput.unit` takes. |

</details>

---

## Mutations

### ruleCreate

Creates a disabled rule. Fails with every violation one pass finds, under `extensions.violations`, when it is invalid.

```graphql
ruleCreate(input: RulesRuleCreateInput!): RulesRulePayload!
    @effect(kinds: [PEOPLE])
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RulesRuleCreateInput!` | The input fields for creating the rule. |

**Input types:**

<details>

<summary>RulesRuleCreateInput</summary>

Input for creating a rule. The rule is created disabled.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | Non-blank, at most 255 characters. |
| `notificationText` | `String` | Sent with the notification instead of the title. Non-blank, at most 1000 characters. Not trimmed. |
| `assetIds` | `[ID!]!` | The assets the rule covers. An empty list covers every asset of the workspace. At most 25 000. |
| `equations` | [[RulesEquationInput](#rulesequationinput)!]! | At most 16. |

</details>

<details>

<summary>RulesEquationInput</summary>

An equation in a write. The fields below belong to one kind each and must be absent for the others:
`field`, `index`, `operator` and `threshold` to PARAMETER, `geoObjectId` and `geofenceRelation` to
GEOFENCE, `scheduleId` and `scheduleRelation` to SCHEDULE. A field of another kind set, or a field
of the equation's own kind missing (`index` excepted), is refused on parse with the field's path.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `connector` | [RulesConnector](#rulesconnector) | How this equation joins the one before it. Required from the second equation on, refused on the first. |
| `kind` | [RulesEquationKind](#rulesequationkind)! |  |
| `geoObjectId` | `ID` | GEOFENCE. A geo object of the caller's workspace with a polygon or multipolygon geometry. |
| `geofenceRelation` | [RulesGeofenceRelation](#rulesgeofencerelation) | GEOFENCE. |
| `scheduleId` | `ID` | SCHEDULE. A schedule of the caller's workspace. |
| `scheduleRelation` | [RulesScheduleRelation](#rulesschedulerelation) | SCHEDULE. |
| `field` | `String` | PARAMETER. The field's name as the telemetry carries it: `^[a-z0-9]+(_[a-z0-9]+)*$`, at most 48 characters, without the channel suffix, which goes in `index`. |
| `index` | `Int` | PARAMETER. The channel of a field that has one, zero or above. Omit for a field without one. |
| `operator` | [RulesOperator](#rulesoperator) | PARAMETER. |
| `threshold` | [RulesThresholdInput](#rulesthresholdinput) | PARAMETER. |

</details>

<details>

<summary>RulesThresholdInput</summary>

A threshold in a write.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `value` | [RulesValueInput](#rulesvalueinput)! | The bound, or the lower bound of a range. |
| `upper` | [RulesValueInput](#rulesvalueinput) | The upper bound of a range, required for IN_RANGE and OUT_OF_RANGE and refused for every other operator. |
| `unit` | `String` | A UCUM code among `km/h`, `Cel`, `L`, `%`, `V`, `kPa`, `h`; only a number takes one. |

</details>

<details>

<summary>RulesValueInput</summary>

A typed value in a write. Exactly one field must be set (`@oneOf`); the field names the type, and
the scalar's own coercion refuses a malformed value.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `number` | [Decimal](../../../core-api-reference/common.md#decimal) | Arbitrary-precision decimal, string-encoded: at most 38 significant digits with the decimal point within 38 places of them. |
| `boolean` | `Boolean` |  |
| `string` | `String` | Compared exactly. Cannot contain the NUL character. |
| `timestamp` | [DateTime](../../../core-api-reference/common.md#datetime) |  |
| `duration` | [Duration](../../../core-api-reference/common.md#duration) | [ISO 8601](https://www.iso.org/standard/8601.html) duration, such as `PT5M`. |

</details>

**Output types:**

<details>

<summary>RulesRulePayload</summary>

The rule a write produced.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `rule` | [RulesRule](#rulesrule)! |  |

</details>

<details>

<summary>RulesRule (entity)</summary>

A rule a workspace wrote over the telemetry of its assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Titled](../../../core-api-reference/common.md#titled), [Versioned](../../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `notificationText` | `String` | The text a notification carries when the rule fires. Absent when the rule sends its title. |
| `coversAllAssets` | `Boolean!` | Whether the rule covers every asset of the workspace, the ones that exist now and the ones added later. |
| `assetIds` | `[ID!]!` | The covered assets the caller may read, in id order; empty when the rule covers all. Assets outside the caller's `bdr.asset` read access are left out and stay attached. |
| `assetCount` | `Int!` | The count of `assetIds`. |
| `equations` | [[RulesEquation](#rulesequation)!]! | The chain, in evaluation order. The first equation carries no connector. |
| `isEnabled` | `Boolean!` | Whether the engine evaluates this rule. A rule is created disabled. |
| `createdAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |

</details>

---

### ruleUpdate

Updates a rule. Validates the resulting rule unless the patch only disables; fails with the violations found.

```graphql
ruleUpdate(input: RulesRuleUpdateInput!): RulesRulePayload!
    @effect(kinds: [PEOPLE])
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RulesRuleUpdateInput!` | The input fields for updating the rule. |

**Input types:**

<details>

<summary>RulesRuleUpdateInput</summary>

Input for updating a rule. `assetIds` and `equations` are the whole lists, not patches over them.
An omitted field keeps its value; `notificationText: null` clears the text; an explicit null on any
other field is refused.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The rule to update. |
| `version` | `Int!` | The current version for optimistic locking. |
| `title` | `String` | Non-blank, at most 255 characters. |
| `notificationText` | `String` | Sent with the notification instead of the title. Non-blank, at most 1000 characters. Not trimmed. Null clears it. |
| `assetIds` | `[ID!]` | The assets the rule covers, replacing the stored list among the assets the caller may read; the stored assets outside the caller's access stay attached. An empty list covers every asset of the workspace, for every caller. At most 25 000. |
| `equations` | [[RulesEquationInput](#rulesequationinput)!] | At most 16. |
| `isEnabled` | `Boolean` | Enabling validates the whole rule; a patch that only disables validates nothing. |

</details>

<details>

<summary>RulesEquationInput</summary>

An equation in a write. The fields below belong to one kind each and must be absent for the others:
`field`, `index`, `operator` and `threshold` to PARAMETER, `geoObjectId` and `geofenceRelation` to
GEOFENCE, `scheduleId` and `scheduleRelation` to SCHEDULE. A field of another kind set, or a field
of the equation's own kind missing (`index` excepted), is refused on parse with the field's path.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `connector` | [RulesConnector](#rulesconnector) | How this equation joins the one before it. Required from the second equation on, refused on the first. |
| `kind` | [RulesEquationKind](#rulesequationkind)! |  |
| `geoObjectId` | `ID` | GEOFENCE. A geo object of the caller's workspace with a polygon or multipolygon geometry. |
| `geofenceRelation` | [RulesGeofenceRelation](#rulesgeofencerelation) | GEOFENCE. |
| `scheduleId` | `ID` | SCHEDULE. A schedule of the caller's workspace. |
| `scheduleRelation` | [RulesScheduleRelation](#rulesschedulerelation) | SCHEDULE. |
| `field` | `String` | PARAMETER. The field's name as the telemetry carries it: `^[a-z0-9]+(_[a-z0-9]+)*$`, at most 48 characters, without the channel suffix, which goes in `index`. |
| `index` | `Int` | PARAMETER. The channel of a field that has one, zero or above. Omit for a field without one. |
| `operator` | [RulesOperator](#rulesoperator) | PARAMETER. |
| `threshold` | [RulesThresholdInput](#rulesthresholdinput) | PARAMETER. |

</details>

<details>

<summary>RulesThresholdInput</summary>

A threshold in a write.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `value` | [RulesValueInput](#rulesvalueinput)! | The bound, or the lower bound of a range. |
| `upper` | [RulesValueInput](#rulesvalueinput) | The upper bound of a range, required for IN_RANGE and OUT_OF_RANGE and refused for every other operator. |
| `unit` | `String` | A UCUM code among `km/h`, `Cel`, `L`, `%`, `V`, `kPa`, `h`; only a number takes one. |

</details>

<details>

<summary>RulesValueInput</summary>

A typed value in a write. Exactly one field must be set (`@oneOf`); the field names the type, and
the scalar's own coercion refuses a malformed value.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `number` | [Decimal](../../../core-api-reference/common.md#decimal) | Arbitrary-precision decimal, string-encoded: at most 38 significant digits with the decimal point within 38 places of them. |
| `boolean` | `Boolean` |  |
| `string` | `String` | Compared exactly. Cannot contain the NUL character. |
| `timestamp` | [DateTime](../../../core-api-reference/common.md#datetime) |  |
| `duration` | [Duration](../../../core-api-reference/common.md#duration) | [ISO 8601](https://www.iso.org/standard/8601.html) duration, such as `PT5M`. |

</details>

**Output types:**

<details>

<summary>RulesRulePayload</summary>

The rule a write produced.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `rule` | [RulesRule](#rulesrule)! |  |

</details>

<details>

<summary>RulesRule (entity)</summary>

A rule a workspace wrote over the telemetry of its assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Titled](../../../core-api-reference/common.md#titled), [Versioned](../../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `notificationText` | `String` | The text a notification carries when the rule fires. Absent when the rule sends its title. |
| `coversAllAssets` | `Boolean!` | Whether the rule covers every asset of the workspace, the ones that exist now and the ones added later. |
| `assetIds` | `[ID!]!` | The covered assets the caller may read, in id order; empty when the rule covers all. Assets outside the caller's `bdr.asset` read access are left out and stay attached. |
| `assetCount` | `Int!` | The count of `assetIds`. |
| `equations` | [[RulesEquation](#rulesequation)!]! | The chain, in evaluation order. The first equation carries no connector. |
| `isEnabled` | `Boolean!` | Whether the engine evaluates this rule. A rule is created disabled. |
| `createdAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |

</details>

---

### ruleDelete

Soft-deletes a rule. Succeeds even when the stored rule is invalid.

```graphql
ruleDelete(input: RulesRuleDeleteInput!): RulesRuleDeletePayload!
    @effect(kinds: [PEOPLE, DATA_LOSS])
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `input` | `RulesRuleDeleteInput!` | The input fields for deleting the rule. |

**Input types:**

<details>

<summary>RulesRuleDeleteInput</summary>

Input for deleting a rule.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The rule to delete. |
| `version` | `Int!` | The current version for optimistic locking. |

</details>

**Output types:**

<details>

<summary>RulesRuleDeletePayload</summary>

The outcome of deleting a rule.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The id of the deleted rule. |

</details>

---

## Objects

<a id="rulesrule"></a>

### RulesRule

A rule a workspace wrote over the telemetry of its assets.

**Implements:** [Node](../../../core-api-reference/common.md#node), [Titled](../../../core-api-reference/common.md#titled), [Versioned](../../../core-api-reference/common.md#versioned)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | A globally unique identifier. This ID is opaque and should not be parsed by clients. |
| `version` | `Int!` | The version number for optimistic locking. Incremented on each update. Can be provided in update/delete mutations to prevent lost updates. If omitted, the update proceeds without stale-read protection. |
| `title` | `String!` | The human-readable display name. |
| `notificationText` | `String` | The text a notification carries when the rule fires. Absent when the rule sends its title. |
| `coversAllAssets` | `Boolean!` | Whether the rule covers every asset of the workspace, the ones that exist now and the ones added later. |
| `assetIds` | `[ID!]!` | The covered assets the caller may read, in id order; empty when the rule covers all. Assets outside the caller's `bdr.asset` read access are left out and stay attached. |
| `assetCount` | `Int!` | The count of `assetIds`. |
| `equations` | [[RulesEquation](#rulesequation)!]! | The chain, in evaluation order. The first equation carries no connector. |
| `isEnabled` | `Boolean!` | Whether the engine evaluates this rule. A rule is created disabled. |
| `createdAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |
| `updatedAt` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |

---

<a id="rulesequation"></a>

### RulesEquation

One link of a rule's chain.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `connector` | [RulesConnector](#rulesconnector) | How this equation joins the one before it. Absent on the first equation. |
| `condition` | [RulesCondition](#rulescondition)! |  |

---

<a id="rulesparametercondition"></a>

### RulesParameterCondition

A telemetry field of the asset's devices compared with a threshold.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | `String!` | The field's name as the telemetry carries it, without the channel suffix. |
| `index` | `Int` | The channel, for a field that has one (`lls_level` with index 2 is `lls_level_2`). Absent otherwise. |
| `operator` | [RulesOperator](#rulesoperator)! |  |
| `threshold` | [RulesThreshold](#rulesthreshold)! |  |

---

<a id="rulesgeofencecondition"></a>

### RulesGeofenceCondition

The asset's position against a geo object.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `geoObjectId` | `ID!` | The geo object; resolves to nothing for a caller who may not read it. |
| `geofenceRelation` | [RulesGeofenceRelation](#rulesgeofencerelation)! |  |

---

<a id="rulesschedulecondition"></a>

### RulesScheduleCondition

The message's time against a schedule.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `scheduleId` | `ID!` | The schedule; resolves to nothing for a caller who may not read it. |
| `scheduleRelation` | [RulesScheduleRelation](#rulesschedulerelation)! |  |

---

<a id="rulesthreshold"></a>

### RulesThreshold

What a parameter condition compares the field with.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `value` | [RulesValue](#rulesvalue)! | The bound, or the lower bound of a range. |
| `upper` | [RulesValue](#rulesvalue) | The upper bound of a range, for IN_RANGE and OUT_OF_RANGE; absent for every other operator. |
| `unit` | `String` | The unit both bounds are given in, as a UCUM code (`km/h`, `Cel`); only a number has one. |

---

<a id="rulesnumbervalue"></a>

### RulesNumberValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `numberValue` | [Decimal](../../../core-api-reference/common.md#decimal)! |  |

---

<a id="rulesboolvalue"></a>

### RulesBoolValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `boolValue` | `Boolean!` |  |

---

<a id="rulesstringvalue"></a>

### RulesStringValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `stringValue` | `String!` |  |

---

<a id="rulestimestampvalue"></a>

### RulesTimestampValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `timestampValue` | [DateTime](../../../core-api-reference/common.md#datetime)! |  |

---

<a id="rulesdurationvalue"></a>

### RulesDurationValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `durationValue` | [Duration](../../../core-api-reference/common.md#duration)! |  |

---

<a id="rulesrulepayload"></a>

### RulesRulePayload

The rule a write produced.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `rule` | [RulesRule](#rulesrule)! |  |

---

<a id="rulesruledeletepayload"></a>

### RulesRuleDeletePayload

The outcome of deleting a rule.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The id of the deleted rule. |

---

<a id="rulesparameterfield"></a>

### RulesParameterField

A field a parameter equation may name, as the devices of the assets report it. `field` and `index` are what
`RulesEquationInput` takes; `valueType` says which member of `RulesValueInput` the threshold sets.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | `String!` | The field's name as the telemetry carries it, without the channel suffix. |
| `index` | `Int` | The channel, for a field that has one (`lls_level` with index 2 is `lls_level_2`). Absent otherwise. |
| `valueType` | [RulesValueType](#rulesvaluetype)! | The type of the field's values; BOOLEAN and STRING take EQ and NE only. |
| `unit` | `String` | The unit the devices label the field with, or the telemetry contract's unit of `board_voltage`, `battery_voltage`, `battery_level` and `hw_mileage` when they send none. Absent for a field without one, and for every type but NUMBER. A threshold may name it only when it is one of the codes `RulesThresholdInput.unit` takes. |

---

## Inputs

<a id="rulesrulefilter"></a>

### RulesRuleFilter

Filtering options for rules. All criteria are ANDed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `titleContains` | `String` | Partial match on title (case-insensitive contains). |
| `isEnabled` | `Boolean` | Only enabled, or only disabled, rules. |
| `assetId` | `ID` | Only rules that apply to this asset: those naming it and those covering all. None for an asset the caller may not read. |

---

<a id="rulesruleorder"></a>

### RulesRuleOrder

Ordering options for rules.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `field` | [RulesRuleOrderField](#rulesruleorderfield) | The standard field to order by. |
| `direction` | [OrderDirection](../../../core-api-reference/common.md#orderdirection)! | The direction to order. |

---

<a id="rulesvalueinput"></a>

### RulesValueInput

A typed value in a write. Exactly one field must be set (`@oneOf`); the field names the type, and
the scalar's own coercion refuses a malformed value.

*This input type uses `@oneOf` - exactly one field must be provided.*

| Field | Type | Description |
| ----- | ---- | ----------- |
| `number` | [Decimal](../../../core-api-reference/common.md#decimal) | Arbitrary-precision decimal, string-encoded: at most 38 significant digits with the decimal point within 38 places of them. |
| `boolean` | `Boolean` |  |
| `string` | `String` | Compared exactly. Cannot contain the NUL character. |
| `timestamp` | [DateTime](../../../core-api-reference/common.md#datetime) |  |
| `duration` | [Duration](../../../core-api-reference/common.md#duration) | [ISO 8601](https://www.iso.org/standard/8601.html) duration, such as `PT5M`. |

---

<a id="rulesthresholdinput"></a>

### RulesThresholdInput

A threshold in a write.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `value` | [RulesValueInput](#rulesvalueinput)! | The bound, or the lower bound of a range. |
| `upper` | [RulesValueInput](#rulesvalueinput) | The upper bound of a range, required for IN_RANGE and OUT_OF_RANGE and refused for every other operator. |
| `unit` | `String` | A UCUM code among `km/h`, `Cel`, `L`, `%`, `V`, `kPa`, `h`; only a number takes one. |

---

<a id="rulesequationinput"></a>

### RulesEquationInput

An equation in a write. The fields below belong to one kind each and must be absent for the others:
`field`, `index`, `operator` and `threshold` to PARAMETER, `geoObjectId` and `geofenceRelation` to
GEOFENCE, `scheduleId` and `scheduleRelation` to SCHEDULE. A field of another kind set, or a field
of the equation's own kind missing (`index` excepted), is refused on parse with the field's path.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `connector` | [RulesConnector](#rulesconnector) | How this equation joins the one before it. Required from the second equation on, refused on the first. |
| `kind` | [RulesEquationKind](#rulesequationkind)! |  |
| `geoObjectId` | `ID` | GEOFENCE. A geo object of the caller's workspace with a polygon or multipolygon geometry. |
| `geofenceRelation` | [RulesGeofenceRelation](#rulesgeofencerelation) | GEOFENCE. |
| `scheduleId` | `ID` | SCHEDULE. A schedule of the caller's workspace. |
| `scheduleRelation` | [RulesScheduleRelation](#rulesschedulerelation) | SCHEDULE. |
| `field` | `String` | PARAMETER. The field's name as the telemetry carries it: `^[a-z0-9]+(_[a-z0-9]+)*$`, at most 48 characters, without the channel suffix, which goes in `index`. |
| `index` | `Int` | PARAMETER. The channel of a field that has one, zero or above. Omit for a field without one. |
| `operator` | [RulesOperator](#rulesoperator) | PARAMETER. |
| `threshold` | [RulesThresholdInput](#rulesthresholdinput) | PARAMETER. |

---

<a id="rulesrulecreateinput"></a>

### RulesRuleCreateInput

Input for creating a rule. The rule is created disabled.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String!` | Non-blank, at most 255 characters. |
| `notificationText` | `String` | Sent with the notification instead of the title. Non-blank, at most 1000 characters. Not trimmed. |
| `assetIds` | `[ID!]!` | The assets the rule covers. An empty list covers every asset of the workspace. At most 25 000. |
| `equations` | [[RulesEquationInput](#rulesequationinput)!]! | At most 16. |

---

<a id="rulesruleupdateinput"></a>

### RulesRuleUpdateInput

Input for updating a rule. `assetIds` and `equations` are the whole lists, not patches over them.
An omitted field keeps its value; `notificationText: null` clears the text; an explicit null on any
other field is refused.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The rule to update. |
| `version` | `Int!` | The current version for optimistic locking. |
| `title` | `String` | Non-blank, at most 255 characters. |
| `notificationText` | `String` | Sent with the notification instead of the title. Non-blank, at most 1000 characters. Not trimmed. Null clears it. |
| `assetIds` | `[ID!]` | The assets the rule covers, replacing the stored list among the assets the caller may read; the stored assets outside the caller's access stay attached. An empty list covers every asset of the workspace, for every caller. At most 25 000. |
| `equations` | [[RulesEquationInput](#rulesequationinput)!] | At most 16. |
| `isEnabled` | `Boolean` | Enabling validates the whole rule; a patch that only disables validates nothing. |

---

<a id="rulesruledeleteinput"></a>

### RulesRuleDeleteInput

Input for deleting a rule.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `ID!` | The rule to delete. |
| `version` | `Int!` | The current version for optimistic locking. |

---

## Enums

<a id="rulesoperator"></a>

### RulesOperator

How a parameter condition compares the field with its threshold.

| Value | Description |
| ----- | ----------- |
| `EQ` |  |
| `NE` |  |
| `GT` |  |
| `GE` |  |
| `LT` |  |
| `LE` |  |
| `IN_RANGE` | Within `value` and `upper`, both inclusive. |
| `OUT_OF_RANGE` | Below `value` or above `upper`. |

---

<a id="rulesconnector"></a>

### RulesConnector

How an equation joins the one before it.

**Values:** `AND`, `OR`

---

<a id="rulesgeofencerelation"></a>

### RulesGeofenceRelation

The asset's position against the geo object.

| Value | Description |
| ----- | ----------- |
| `INSIDE` |  |
| `OUTSIDE` |  |
| `ENTRY` | Inside now and outside at the previous position. |
| `EXIT` | Outside now and inside at the previous position. |

---

<a id="rulesschedulerelation"></a>

### RulesScheduleRelation

The message's time against the schedule.

**Values:** `WITHIN`, `OUTSIDE`

---

<a id="rulesequationkind"></a>

### RulesEquationKind

Which condition an equation in a write holds.

**Values:** `PARAMETER`, `GEOFENCE`, `SCHEDULE`

---

<a id="rulesruleorderfield"></a>

### RulesRuleOrderField

Fields available for ordering rules.

| Value | Description |
| ----- | ----------- |
| `TITLE` | Order by title. |
| `CREATED_AT` | Order by creation time. |
| `UPDATED_AT` | Order by last modification time. |

---

<a id="rulesvaluetype"></a>

### RulesValueType

The type of a field's values, which decides the threshold's member of `RulesValueInput` and the operators.

**Values:** `NUMBER`, `BOOLEAN`, `STRING`, `TIMESTAMP`, `DURATION`

---

## Unions

<a id="rulescondition"></a>

### RulesCondition

What an equation asks. No field name repeats across the members (the relations are `geofenceRelation`
and `scheduleRelation`), so a selection reading several members needs no aliases.

**One of:** [RulesParameterCondition](#rulesparametercondition), [RulesGeofenceCondition](#rulesgeofencecondition), [RulesScheduleCondition](#rulesschedulecondition)

---

<a id="rulesvalue"></a>

### RulesValue

A typed value: a threshold's bound, or a value an equation was satisfied with. Each member names
its field after the kind of value it holds (`numberValue`, `boolValue`, …), as `TrackingValue`'s
members do, so a selection reading several members needs no aliases.

**One of:** [RulesNumberValue](#rulesnumbervalue), [RulesBoolValue](#rulesboolvalue), [RulesStringValue](#rulesstringvalue), [RulesTimestampValue](#rulestimestampvalue), [RulesDurationValue](#rulesdurationvalue)

---

## Pagination types

<a id="rulesruleconnection"></a>

### RulesRuleConnection

A paginated list of RulesRule items.

**Implements:** [Connection](../../../core-api-reference/common.md#connection)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `edges` | [[RulesRuleEdge](#rulesruleedge)!]! | A list of edges. |
| `nodes` | [[RulesRule](#rulesrule)!]! | A list of nodes in the connection (without edge metadata). |
| `pageInfo` | [PageInfo](../../../core-api-reference/common.md#pageinfo)! | Information about the current page. |
| `total` | [CountInfo](../../../core-api-reference/common.md#countinfo) | The total count of items matching the filter. |

---

<a id="rulesruleedge"></a>

### RulesRuleEdge

An edge in the RulesRule connection.

**Implements:** [Edge](../../../core-api-reference/common.md#edge)

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cursor` | `String!` | An opaque cursor for this edge. |
| `node` | [RulesRule](#rulesrule)! | The rule at the end of the edge. |

---

## See also

* [Pagination](../../../pagination.md)
* [Filtering and sorting](../../../filtering-and-sorting/README.md)
* [Error handling](../../../error-handling.md)
* [Optimistic locking](../../../optimistic-locking.md)
* [Limits](../../../limits.md)
