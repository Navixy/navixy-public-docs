---
description: How a vehicle sets a diagnostic trouble code, how OBD-II and J1939 codes are built, and how Navixy shows the codes that GPS devices report.
tags:
  - fundamentals
---

# Diagnostic trouble codes (DTCs)

A diagnostic trouble code (DTC) is a short code that a vehicle stores when its diagnostic system detects a fault. This page explains how a vehicle sets a code and how to read the structure of a code. It's for fleet managers and technicians who see fault codes in Navixy and need to know what they're looking at.

## What a diagnostic trouble code is

A DTC names a fault that a vehicle's control units detected. Each control unit, such as the engine or transmission control unit, checks its sensors and circuits. When a reading stays outside its expected range, the control unit stores a code for the fault. A diagnostic tool or a GPS device can read the code later through the vehicle's diagnostic port.

Cars and light trucks use the OBD-II code format. OBD-II (on-board diagnostics, second generation) became a requirement for new cars and light trucks in the United States from the 1996 model year. The European Union requires a similar system, called EOBD. OBD-II was first designed to detect faults that raise exhaust emissions. Vehicles now also store codes for the body, the chassis, and the network between control units.

Trucks, buses, and heavy equipment often use the SAE J1939 standard instead. Their codes have a different form, described in [J1939 codes in trucks and heavy equipment](#j1939-codes-in-trucks-and-heavy-equipment).

## How a vehicle sets a code

A vehicle sets a code only for a fault that lasts for some time or comes back. A short glitch, such as one bad reading at engine start, usually produces no code. The rule prevents false alarms.

A confirmed code turns on the malfunction indicator lamp (MIL), also called the check engine light. For many emission-related faults, the process has two steps:

1. The first failed test sets a pending code. A pending code doesn't turn on the MIL.
2. If the same test fails again on the next drive, the code becomes confirmed, and the MIL turns on.

Some serious faults set a confirmed code and turn on the light on the first failure. Not every code turns on the light: the MIL is meant for faults that affect emissions, so many body, chassis, and network codes don't turn it on.

The light doesn't always stay on until a repair. Under OBD-II rules, the light turns off when the fault doesn't come back for three drives in a row. A scan tool can also clear the codes and turn off the light. In both cases, the fault can return if nobody repairs its cause.

A vehicle can store several codes at the same time.

## How an OBD-II code is built

An OBD-II code has five characters, such as `P0301`. The SAE J2012 standard defines the format, and ISO 15031-6 is its international equivalent. Each part of the code tells you something:

| Position | Example | What it tells you |
| --- | --- | --- |
| 1 | `P` | The system: `P` for powertrain, `B` for body, `C` for chassis, `U` for network. |
| 2 | `0` | Who defines the code: the standard or the vehicle manufacturer. |
| 3 | `3` | The subsystem. In `P0` codes, `3` is the ignition system and misfires. |
| 4 and 5 | `01` | The specific fault. In `P0301`, it's a misfire in cylinder 1. |

The second character is a digit from 0 to 3. A `0` always marks a generic code, which means the same thing on every vehicle. A `1` always marks a code that the manufacturer defines. The values `2` and `3` split between the standard and manufacturers in different ways for each system letter. For example, `P2` codes are generic, and `B2` codes belong to manufacturers.

The last three characters are hexadecimal, so they can be the letters A to F as well as digits. For example, generic powertrain codes that start with `P0A` cover hybrid vehicle systems. The subsystem meanings also differ between system letters and between the generic and manufacturer ranges.

Some diagnostic tools show two more characters after the five, such as `P0115-11`. These characters name the type of failure, for example a short circuit to ground. The first five characters keep their meaning.

## J1939 codes in trucks and heavy equipment

A J1939 code doesn't use the five-character OBD-II form. Instead, it has three numbers:

* **SPN (suspect parameter number)**: The parameter or component that has the fault. For example, SPN 110 is the engine coolant temperature.
* **FMI (failure mode identifier)**: The type of failure, from 0 to 31. For example, FMI 0 means that the value is valid but above the normal range, at the most severe level.
* **OC (occurrence count)**: How many times the fault occurred.

The vehicle reports active faults in a message called DM1 and faults that are no longer active in a message called DM2. The J1939 standard defines the FMIs and most SPNs. It also leaves a range of SPNs for manufacturers to define.

## Where to find what a code means

Look up a generic OBD-II code in a reference for generic OBD-II codes, such as SAE J2012 or a service information system. Look up a manufacturer-specific code, or a manufacturer-specific SPN, in the manufacturer's service documentation. Generic references don't cover manufacturer-specific codes, and the same number can mean different faults on different makes.

A code tells you which test failed, not which part to replace. A misfire code, for example, can come from a spark plug, an ignition coil, a fuel injector, or a wiring fault. A technician confirms the cause before a repair.

## Fault codes in Navixy

A GPS device that is connected to the vehicle reads the codes and sends them to the Navixy platform. No sensor setup is needed. Which codes the device reads, for example only confirmed codes or pending codes too, depends on the GPS device and its settings.

Navixy stores the codes exactly as the device sends them. It doesn't filter or decode the codes and has no list of code meanings. To learn what a code means, look it up as described in [Where to find what a code means](#where-to-find-what-a-code-means).

The **Conditions, 14 days** dashboard lists the fault codes of the last 14 days, without a description. When several codes are active, the dashboard shows them together in one **DTC code** cell, separated by commas. When a GPS device sends J1939 codes, they appear in the form SPN:FMI:OC, for example `110:0:3`, not in the five-character OBD-II form. To learn what the dashboard shows, see [Fault codes](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/dashboards/conditions#fault-codes).

## See also

* [OBD device fundamentals](obd-tracker-fundamentals.md): Learn how OBD devices connect to the diagnostic port and what data they read.
* [Intra-vehicle communication: CAN, FlexRay, and MOST](intra-vehicle-communication-can-flexray-and-most.md): Learn how control units exchange data inside a vehicle.
