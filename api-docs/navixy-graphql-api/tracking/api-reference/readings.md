---
description: >-
  Reference for tracking readings: the value model, the reading and its unit
  rules, and the field dictionary with the queries that expose it.
---

# Readings and the field dictionary

{% include "../../.gitbook/includes/navixy-graphql-api-is-a-....md" %}

How a reading is encoded, the reading itself, and the field dictionary that gives each reading its title, unit and nature.

## Queries

### hotFieldCodes

The fields the service keeps hot — the ones `TrackingDeviceState.readings` returns when no
  `codes` argument is given, and the ones a subscription can deliver on every message.

  A deployment setting, not a dictionary property: NTF SPEC §5.4 forbids changing a dictionary
  entry, and which fields are worth holding in memory is a property of this service's
  configuration. Exposed so a client can tell the two tiers apart instead of inferring the
  boundary from what happens to arrive.

```graphql
hotFieldCodes: [Code!]!
```

**Output types:**

<details>

<summary>Code</summary>

A machine-readable identifier code.

Constraints:
- Allowed characters: ASCII letters (a-z, A-Z), digits (0-9), underscore (_), dot (.), hyphen (-)
- Must start with a letter or digit
- Case-preserving: the original casing is stored as provided
- Case-insensitive for uniqueness checks and comparisons
- Length: 1 to 100 characters (auto-generated codes are truncated to 30)

Uniqueness:
- For catalog items: unique within the same catalog and workspace scope
- For custom field definitions: unique per owner catalog item and workspace
- For field options (OPTIONS type): unique within a single field definition
- Additional uniqueness requirements may apply depending on context (see individual fields)

Examples: DEVICE_TYPE, vehicle_car, status.active, sensor-v2, ABC123

</details>

---

### fieldDefinitions

The field dictionary. Needed by any UI that renders readings: without it a client has a name
  and a number but no title, unit or nature.

  Cacheable, but not immutable. What NTF freezes is an entry's identity — SPEC §5.4 forbids
  changing a type, unit or nature, and an entry is never renamed — so a cached entry never starts
  meaning something else. What it does not freeze is the dictionary's composition: SPEC §5.1 says
  it changes by design, and an existing entry can still gain `deprecated` and a `replacedBy`. So a
  held copy goes stale in one direction only — it can lack an entry, or miss a deprecation, never
  misdescribe a field it already has. Refetch when a reading arrives whose `code` is not in the
  cache. The locale is separate: it comes from the request headers rather than an argument here, so
  a client that switches language re-fetches, and a shared cache is keyed per locale or holds only
  the locale-independent fields.

```graphql
fieldDefinitions(
    codes: [Code!]
    includeDeprecated: Boolean = false
  ): [TrackingFieldDefinition!]!
```

**Arguments**

| Name | Type | Description |
| ---- | ---- | ----------- |
| `codes` | [[Code](../../core-api-reference/common.md#code)!] | Limit to these field names. All entries when omitted. |
| `includeDeprecated` | `Boolean` | Include entries marked deprecated. |

**Output types:**

<details>

<summary>TrackingFieldDefinition</summary>

A field dictionary entry (NTF SPEC §5). Entries are never renamed, retyped or deleted — only
deprecated with a pointer to the replacement, because an entry describes every value ever
recorded under that name, not just new ones.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../core-api-reference/common.md#code)! |  |
| `title` | `String!` | Human-readable name of the field, for a UI that renders readings. Localized: the language comes from the request, not from an argument — `X-Locale` or `Accept-Language`, with English as the fallback, per §3 Localization in nvx4-api.graphql. So this field is the one part of a dictionary entry that is not stable across callers, and a client caching the dictionary caches it per locale. |
| `description` | `String` | Longer explanation of the field, localized on the same terms as `title`. |
| `valueType` | [TrackingValueType](#trackingvaluetype)! |  |
| `unit` | `String` | Reference unit (UCUM): the one the quantity is customarily shown and stored in (NTF SPEC §3.2). Not a default for a value that arrived without one — `TrackingReading.unit` says when it stands in. Null for dimensionless fields. |
| `quantity` | [TrackingQuantity](#trackingquantity) |  |
| `nature` | [TrackingFieldNature](#trackingfieldnature)! |  |
| `channelled` | `Boolean!` | Whether values of this field can carry a channel number. |
| `computed` | `Boolean!` | Always computed by the platform rather than measured (e.g. `mileage`). |
| `counterScope` | [TrackingCounterScope](#trackingcounterscope) | For COUNTER fields: what the count runs from. |
| `resettable` | `Boolean` | For COUNTER fields: whether the counter can legitimately be reset (vs. wrapping around). |
| `deprecated` | `Boolean!` |  |
| `replacedBy` | [Code](../../core-api-reference/common.md#code) |  |
| `aliases` | [[Code](../../core-api-reference/common.md#code)!]! | Field names this entry absorbed on the nvx3 → NTF migration. |

</details>

---

## Objects

<a id="trackingboolvalue"></a>

### TrackingBoolValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `boolValue` | `Boolean!` |  |

---

<a id="trackingintvalue"></a>

### TrackingIntValue

Signed integer of any width (int8…int64).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `intValue` | [Long](../../core-api-reference/common.md#long)! |  |

---

<a id="trackinguintvalue"></a>

### TrackingUIntValue

Unsigned integer of any width (uint8…uint64). Values above 2^63-1 — bitmasks, odometer-style
counters, identifiers — do not fit `Long`, so the value rides the kernel's `Decimal` scalar,
which already exists with a coercion and a validation path. A bare `String` would push the same
parse onto the client with nothing checking it.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `uintValue` | [Decimal](../../core-api-reference/common.md#decimal)! |  |

---

<a id="trackingfloatvalue"></a>

### TrackingFloatValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `floatValue` | `Float!` |  |

---

<a id="trackingstringvalue"></a>

### TrackingStringValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `stringValue` | `String!` |  |

---

<a id="trackingbytesvalue"></a>

### TrackingBytesValue

Opaque binary payload (raw CAN frame, RFID key), Base64-encoded.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `bytesValue` | `String!` |  |

---

<a id="trackingtimestampvalue"></a>

### TrackingTimestampValue

| Field | Type | Description |
| ----- | ---- | ----------- |
| `timestampValue` | [DateTime](../../core-api-reference/common.md#datetime)! |  |

---

<a id="trackingdurationvalue"></a>

### TrackingDurationValue

A duration. Carried by the kernel's `Duration` scalar, which this module introduces to
`nvx4-common.graphql` — see the note at the head of this file. Every duration in this schema uses
it, so `"5m"` instead of `PT5M` is rejected by coercion rather than by a resolver that may or may
not check.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `durationValue` | [Duration](../../core-api-reference/common.md#duration)! |  |

---

<a id="trackingarrayvalue"></a>

### TrackingArrayValue

Homogeneous list (DTC codes, tachograph cards, acceleration vector).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `values` | [[TrackingValue](#trackingvalue)!]! |  |

---

<a id="trackingstructvalue"></a>

### TrackingStructValue

Nested structure whose members are full readings (decoded J1939 frame, tachograph block).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `fields` | [[TrackingReading](#trackingreading)!]! |  |

---

<a id="trackingclearedvalue"></a>

### TrackingClearedValue

The value was explicitly cleared. Its own variant so that "nothing was said" and "this no longer
holds" can never be confused (NTF SPEC §3.3).

| Field | Type | Description |
| ----- | ---- | ----------- |
| `cleared` | `Boolean!` |  |

---

<a id="trackingreading"></a>

### TrackingReading

One named field of a state — a sensor reading, a device state, a counter, an identity.
There is exactly one such list per state: NTF v1 has no inputs/states split.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../core-api-reference/common.md#code)! | Dictionary name of the field, e.g. `lls_level`, `ignition`, `hw_mileage`. |
| `channel` | `Int` | Channel number for multi-channel fields (`lls_level` on tank 2). Null when the field has none. |
| `value` | [TrackingValue](#trackingvalue)! |  |
| `unit` | `String` | Unit of this value (UCUM, e.g. `km`, `Cel`, `L/h`), by the rules of NTF SPEC §3.2: the unit the message carried. For the four legacy fields the format names — `board_voltage`, `battery_voltage`, `battery_level`, `hw_mileage` — a message without a unit means the dictionary's unit by contract, and this field carries it. For every other field a message without a unit means "unknown", and this field is null: a dictionary `Cel` on `temperature` does not make a number that arrived without a unit degrees Celsius. So null covers both "dimensionless" and "unknown". `definition.unit` tells the two apart when the code is in the dictionary — a dimensionless field has none there. For a code the dictionary does not know, `definition` is null as well and the two cannot be told apart: the value has no unit a client may rely on. `definition.unit` is a reference value — the unit the quantity is customarily shown in — never a default for this one. |
| `declaredType` | [TrackingValueType](#trackingvaluetype) | Source-register type, when the sender declared one or the dictionary knows it. |
| `updatedAt` | [DateTime](../../core-api-reference/common.md#datetime)! | When this value was last received. Per NTF SPEC §3.5 a field carries no timestamp of its own — this is the event time of the message it last arrived in, which across a last-known state is *not* the same for every reading. |
| `definition` | [TrackingFieldDefinition](#trackingfielddefinition) | Dictionary entry behind `code`: title, nature, quantity, deprecation. |

---

<a id="trackingfielddefinition"></a>

### TrackingFieldDefinition

A field dictionary entry (NTF SPEC §5). Entries are never renamed, retyped or deleted — only
deprecated with a pointer to the replacement, because an entry describes every value ever
recorded under that name, not just new ones.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `code` | [Code](../../core-api-reference/common.md#code)! |  |
| `title` | `String!` | Human-readable name of the field, for a UI that renders readings. Localized: the language comes from the request, not from an argument — `X-Locale` or `Accept-Language`, with English as the fallback, per §3 Localization in nvx4-api.graphql. So this field is the one part of a dictionary entry that is not stable across callers, and a client caching the dictionary caches it per locale. |
| `description` | `String` | Longer explanation of the field, localized on the same terms as `title`. |
| `valueType` | [TrackingValueType](#trackingvaluetype)! |  |
| `unit` | `String` | Reference unit (UCUM): the one the quantity is customarily shown and stored in (NTF SPEC §3.2). Not a default for a value that arrived without one — `TrackingReading.unit` says when it stands in. Null for dimensionless fields. |
| `quantity` | [TrackingQuantity](#trackingquantity) |  |
| `nature` | [TrackingFieldNature](#trackingfieldnature)! |  |
| `channelled` | `Boolean!` | Whether values of this field can carry a channel number. |
| `computed` | `Boolean!` | Always computed by the platform rather than measured (e.g. `mileage`). |
| `counterScope` | [TrackingCounterScope](#trackingcounterscope) | For COUNTER fields: what the count runs from. |
| `resettable` | `Boolean` | For COUNTER fields: whether the counter can legitimately be reset (vs. wrapping around). |
| `deprecated` | `Boolean!` |  |
| `replacedBy` | [Code](../../core-api-reference/common.md#code) |  |
| `aliases` | [[Code](../../core-api-reference/common.md#code)!]! | Field names this entry absorbed on the nvx3 → NTF migration. |

---

## Enums

<a id="trackingvaluetype"></a>

### TrackingValueType

Declared type of a value in terms of the *source* register. `navixy.telemetry.v1.ValueType`.

**Values:** `BOOL`

---

<a id="trackingfieldnature"></a>

### TrackingFieldNature

What kind of quantity a field is, which decides how it may be aggregated (NTF SPEC §5.2).
Dictionary metadata, not carried by the message.

| Value | Description |
| ----- | ----------- |
| `GAUGE` | Instantaneous measurement — averaging and medians are meaningful. |
| `COUNTER` | Monotonic accumulator; see `counterScope` and `resettable`. |
| `DELTA` | Increment since the previous report. |
| `STATE` | Holds until it changes — ignition, doors. Averaging is meaningless. |
| `BITSET` | Raw bitmask as sent by the device; bit layout belongs to the device model, not the field. |
| `IDENTITY` | Identity — VIN, driver card, ICCID. |
| `TEXT` | Free-form text. |
| `SUMMARY_MIN` | Summary over an interval whose bounds the device does not report. |
| `SUMMARY_MAX` |  |
| `SUMMARY_AVG` |  |
| `SETTING` | Device configuration value. |

---

<a id="trackingquantity"></a>

### TrackingQuantity

Application-level quantity, from the field dictionary. Drives unit conversion and formatting.

---

<a id="trackingcounterscope"></a>

### TrackingCounterScope

**Values:** `LIFETIME`, `TRIP`, `IGNITION_CYCLE`, `SINCE_RESET`

---

## Unions

<a id="trackingvalue"></a>

### TrackingValue

How a reading is encoded. Mirrors `navixy.telemetry.v1.Value.value` — eight scalar variants,
two composite ones, and an explicit clear.

An absent variant means the sender had nothing to say and the previous value stands;
`TrackingClearedValue` is the opposite — an assertion that the value no longer holds (driver key
pulled, trailer detached). NTF SPEC §3.3.

Each scalar variant names its field after the type — `boolValue`, `intValue`, … — instead of all
sharing `value`. A selection set that reads `value` from two members with different scalars fails
validation (`FieldsInSetCanMerge`, GraphQL spec §5.3.2), so a client rendering readings would have
to alias the occurrences. docs/reference/graphql-conventions.md §Interface field naming resolves
the same conflict for interfaces with `<semantic><Variant>` (`minDecimal`); here the order inside a
name is the wire's — `bool_value`, `int_value`, … in `value.proto` — so a schema name matches the
JSON member name in the NTF SPEC §9 examples, except that `double_value` is `floatValue`, after the
GraphQL scalar and this type's name. `GraphqlSchemaValidationTest$UnionMemberFieldShapes` enforces
the distinct-name half for every union.

**One of:** [TrackingBoolValue](#trackingboolvalue), [TrackingIntValue](#trackingintvalue), [TrackingUIntValue](#trackinguintvalue), [TrackingFloatValue](#trackingfloatvalue), [TrackingStringValue](#trackingstringvalue), [TrackingBytesValue](#trackingbytesvalue), [TrackingTimestampValue](#trackingtimestampvalue), [TrackingDurationValue](#trackingdurationvalue), [TrackingArrayValue](#trackingarrayvalue), [TrackingStructValue](#trackingstructvalue), [TrackingClearedValue](#trackingclearedvalue)

---

## See also

* [Pagination](../../pagination.md)
* [Filtering and sorting](../../filtering-and-sorting/README.md)
* [Error handling](../../error-handling.md)
* [Optimistic locking](../../optimistic-locking.md)
* [Limits](../../limits.md)
