---
description: Configure the Engine state detection block in Navixy so the device reports engine on and off based on on-board voltage levels and durations.
---

# Engine state detection

Tells the device how to determine that the engine is running, based on on-board voltage. A running engine raises system voltage above the battery's resting level, typically from around 12 V at rest to 13–14 V when alternator charging.

## Settings

* **Engine-on voltage threshold**: the voltage level above which the engine is considered running. Typical value: 13.0–13.5 V.
* **Engine-on duration**: how long the voltage must stay above the threshold before the engine-on state is confirmed. A short duration (2–3 seconds) prevents false triggers from brief voltage spikes.
* **Engine-off voltage threshold**: the voltage level below which the engine is considered off. Typical value: 12.5 V.
* **Engine-off duration**: how long the voltage must stay below the threshold before the engine-off state is confirmed.

{% hint style="info" %}
Set the engine-off threshold a few tenths of a volt below the engine-on threshold to create a small gap between the on and off thresholds so the engine state doesn't flip back and forth when the voltage wavers at idle.
{% endhint %}

## Availability

Appears on device models that detect engine state from on-board voltage.

## See also

* [Ignition source](../../location-and-movement/ignition-source.md): choose how the Navixy platform interprets ignition.
* [Fuel consumption calculation](fuel-consumption-calculation.md): device-side fuel estimate that depends on engine state.
