---
description: >-
  How battery condition, charging, range, and cost affect an electric vehicle
  fleet, and which EV values GPS devices can send to Navixy.
tags:
  - fundamentals
---

# Electric vehicle fleet management

This page explains what changes when a fleet runs electric vehicles (EVs): battery condition, charging, range, and cost. It also explains which EV values a GPS device can send to the Navixy platform. It is for fleet managers who plan to add EVs or who already run them.

## Types of electric vehicles

Three types of vehicle use an electric motor. They differ in where the motor gets its energy:

* **Battery electric vehicle (BEV)**: Runs only on electricity from a large battery that charges from the grid. It has no combustion engine.
* **Plug-in hybrid electric vehicle (PHEV)**: Has a battery that charges from the grid and a gasoline or diesel engine. It drives short distances on electricity and uses the engine when the battery runs low.
* **Hybrid electric vehicle (HEV)**: Has an electric motor and a combustion engine but no charging port. The engine charges its battery, and so does energy recovered when the vehicle brakes.

BEVs and PHEVs both need charging, and BEVs depend on it completely. The rest of this page applies mainly to BEVs.

## Battery state of charge and state of health

Two values describe an EV battery. The state of charge (SoC) is the energy left in the battery, as a percentage of its capacity. It does the job of a fuel gauge: it tells you how far a vehicle can still go and when it needs to charge.

The state of health (SoH) compares the battery's present capacity with its capacity when new. A battery with an SoH of 90% stores about 90% of the energy that it stored when new. The vehicle's range on a full charge is shorter by about the same share.

Every battery loses capacity over time. These factors make it lose capacity faster:

* Many full charge and discharge cycles
* High temperatures, especially for long periods
* Long periods at a very high or a very low state of charge
* Frequent DC fast charging, which heats the battery
* Age, because the battery's chemistry changes even when the vehicle stands still

To slow this loss, many manufacturers recommend a daily charge limit below 100% and a full charge only before long trips. The right limit depends on the battery chemistry, so follow the vehicle manufacturer's guidance.

## Charging levels and connectors

Charging speed depends on the charger's power and on how much power the vehicle accepts. North America uses three charging levels. The US Department of Energy gives these approximate figures:

| Level | Power source | Power | Range added |
| --- | --- | --- | --- |
| Level 1 | 120 V AC household outlet | Up to about 2 kW | About 5 miles (8 km) per hour |
| Level 2 | 208 V or 240 V AC | About 3 to 19 kW, often 7 kW | About 25 miles (40 km) per hour |
| DC fast charging | Direct current from a dedicated station | Up to about 500 kW | About 100 to 200 miles (160 to 320 km) per 30 minutes |

Outside North America, chargers are usually described as AC or DC. AC charging in Europe often uses three-phase power, commonly at 11 kW or 22 kW. DC fast charging slows down as the battery fills, so a fast-charging session often ends at about 80%.

The connector depends on the region and on the type of charging:

* **North America**: J1772 for AC charging and CCS1 for DC charging. Many new vehicles and stations use the SAE J3400 connector, also called NACS, for both.
* **Europe, the UK, and Australia**: Type 2 for AC charging and CCS2 for DC charging.
* **China**: GB/T, with separate ports for AC and DC charging.
* **Japan**: CHAdeMO for DC charging.

Check which connectors your vehicles accept before you choose chargers for a depot.

## Factors that affect EV range

Real range on a route often differs from the rated range, which comes from standard tests. These factors change it most:

* **Temperature**: In cold weather, heating the cabin and the battery uses energy. Tests reported by the US Department of Energy at about -7 °C (20 °F) found range about 41% lower than at about 24 °C (75 °F) with the cabin heater on. Without the heater, range was about 12% lower.
* **Speed**: Air resistance grows quickly with speed, so highway driving uses more energy per kilometer than city driving.
* **Payload and terrain**: Heavy loads and steep climbs use more energy. Regenerative braking recovers part of it on descents: the motor turns the vehicle's motion back into electricity.
* **Driving style**: Hard acceleration uses more energy, and smooth braking recovers more.

## Total cost of ownership for EVs

Total cost of ownership is the full cost of a vehicle over its years in the fleet. For an EV, the costs split differently from a combustion vehicle:

* **Purchase**: An EV often costs more to buy than a comparable combustion vehicle. Purchase incentives differ by country and change often.
* **Energy**: In most regions, electricity costs less per kilometer than fuel, especially when vehicles charge overnight at the depot. Public DC fast charging costs more than depot charging.
* **Maintenance**: EVs have no engine oil, spark plugs, or exhaust system, and regenerative braking reduces brake wear. Tires, brakes, the cooling system, and the 12 V battery still need service.
* **Chargers**: Depot chargers need equipment and installation. Some sites also need a larger electrical supply.
* **Battery**: Manufacturers cover the high-voltage battery with its own warranty, often for 8 years or 100,000 miles (about 160,000 km). The warranty usually promises that the battery keeps a minimum share of its original capacity, commonly 70%.

## EV data from GPS devices

Some GPS devices read EV data from the vehicle's CAN bus, the network that connects the vehicle's electronic units. Others read it from the battery management system (BMS), the unit that monitors and protects the battery. These devices can send EV values to Navixy, such as:

* State of charge
* Charger voltage and current
* Whether the charger is plugged in

Navixy stores these values as readings from the device. The [Sensor readings](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/reports/sensor-readings) report can show the numeric ones over a period.

Which values a device sends depends on the device model, its settings, and the vehicle. Before you install a device, check with its manufacturer that it reads EV data from your vehicle models.

A state-of-charge history shows when each vehicle charged, how much charge each shift used, and which vehicles started the day with a low charge. Charger readings show whether a vehicle was plugged in and whether it charged.

## See also

* [Intra-vehicle communication: CAN, FlexRay, and MOST](../can-and-obdii/intra-vehicle-communication-can-flexray-and-most.md): Learn how the electronic units in a vehicle exchange data over the CAN bus.
* [OBD device fundamentals](../can-and-obdii/obd-tracker-fundamentals.md): Learn how plug-in OBD devices read vehicle data, including in electric vehicles.
