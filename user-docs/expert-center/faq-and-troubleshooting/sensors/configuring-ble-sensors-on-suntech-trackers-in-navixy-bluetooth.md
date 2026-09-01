# Configuring BLE Sensors on Suntech Trackers in Navixy Bluetooth

Bluetooth Low Energy (BLE) sensors let you monitor temperature, humidity, door state, and panic events without extra wiring. A Suntech tracker with a BLE gateway scans those sensors and sends the values to Navixy inside its normal status and alert reports.

This article covers the sensors: how to prepare Minew beacons, how to pair them in SyncTrak, and how to show the data in Navixy. For ignition, digital inputs, outputs, and Fine Tracking intervals, use the Suntech device setup article.

Compatible Suntech families (BLE gateway required):

* ST4300 series, for example ST4315B, ST4345LB
* ST4900 series, for example ST4932, ST4955LCBW, ST4915LCBF
* ST8000 series, for example SD8000U, ST8230

Confirm BLE support and the matching Navixy model for your exact tracker before you start. The parameter examples below come from an ST4315B. Slot numbers can differ on other models if Report Mapping uses a different field order

## 1. Prepare the Sensors with the Minew Apps

The tracker only reads what each sensor already broadcasts. Configure the sensor first. Write down the MAC address of every sensor. You need it in SyncTrak.

Install BeaconSET+ for the wristband and the temperature and humidity sensor. Install MSensor for the door sensor. Enable Bluetooth, open the app, and select the sensor by MAC address.

### Sensor Setup via Mobile Apps

#### Minew B10 Wristband (Panic Button): Use BeaconSET+ app  (default PIN: minew123 or 000000).



* Slot 1 iBeacon: Base params OFF.
* Trigger Params: ON. Enable tap types (single, double, triple).
* Trigger type: Enable single, double, and triple tap if the app offers them.&#x20;
* Advertising time after the trigger: 10 seconds. Use 10 seconds of advertising. With 1 second, the button often stops before the tracker scans, and the panic event is lost
* Interval & Power: 900 ms, 0 dBm.<br>

The tracker reports panic as event 136. The subtype follows the number of clicks:&#x20;

* One click: Packet 136;1 (Navixy sub\_event\_code: 136\_1)&#x20;
* Two clicks: Packet 136;2 (Navixy sub\_event\_code: 136\_2) &#x20;
* Three clicks: Packet 136;3 (Navixy sub\_event\_code: 136\_3) <br>

#### Minew Temperature & Humidity Sensor: Use BeaconSET+ (default PIN: minew123).

* Slot 1 iBeacon: Base params OFF (iBeacon alone carries no telemetry).
* Telemetry Slot (SLOT4 TLM / HT): Base params ON.
* Interval & Power: 900–1000 ms, 0 dBm.

#### Minew S4 Door Sensor: Use MSensor (default PIN: minewtech1234567).

* Operating Mode: Regular mode (Do NOT use Energy-saving mode, or door events will be delayed/dropped).
* Interval & Power: 900–1000 ms, 0 dBm.

Crucial: A BLE sensor accepts one connection at a time. While the phone app stays connected, the tracker cannot read that sensor. Close BeaconSET+ or MSensor, or move out of range, after you save the parameters

### Pairing Sensors in SyncTrak

Once the sensors are broadcasting, open the SyncTrak desktop tool to configure the tracker's BLE scanning rules

#### BLE Parameter Tab:

* BLE SCAN (4021): Enable
* Sensor RPT BLE scan time (4011): 20 seconds&#x20;
* Continuous scan (4037): Enable Keep Continuous scan enabled when you use a panic button or a door sensor. If you disable it, the BLE receiver sleeps between scan windows. A press or a door change in that window is dropped. Temperature and humidity can still update.&#x20;
* BDA RPT BLE scan time (4012): Set lower than Fine Tracking time. If the scan lasts longer than the report interval, the tracker aborts the scan to send the report on time. The BLE report then shows scan status 0 and sensor count 0.

#### Register BLE Tab:&#x20;

* Add the wristband and the climate sensor here. Sensor type: 5: MINEW. Click ADD, enter the MAC address, and save.
* The registration order matters. The first sensor is dedicated BLE 1, the second is dedicated BLE 2. Report fields such as DED\_BLE1\_TEMP follow that order. Keep the order stable.
* Set In alert to Enable only when you use the high and low thresholds of that sensor. Do not register the door sensor only on this tab

#### Register Door  Tab:&#x20;

* &#x20;Register the door sensor here. Sensor type: 4: MINEW DOOR. Enable door sensor for that point.
* The firmware keeps door peripherals in a separate list. A door sensor that exists only under Register BLE sends no door state. The same MAC can appear on both tabs without harm, but Register Door is the entry that works.

#### BLE Sensor Config Tab:&#x20;

* Set BLE sensor report (2610) to Stt/Alt scan enable so BLE values attach to standard reports.

<img src="../../.gitbook/assets/unknown (36).png" alt="SyncTrak BLE Parameter and Register tabs configured for continuous scanning and sensor pairing. " height="269" width="624">

#### Enable BLE alerts in SyncTrak

On the alert configuration tabs, enable the events you will use in Navixy:

* 119: Dedicated BLE sensor in range
* 120: Dedicated BLE sensor out of range
* 121 / 122: Dedicated BLE temperature high / low
* 124 / 125: Dedicated BLE humidity high / low
* 135: Dedicated BLE door state
* 136: Dedicated BLE panic state

#### Report Mapping tab

Navixy does not read the raw BDA BLE report. Select the BLE fields in Report Mapping so they travel inside STT and ALT.

Select at least:

* Temperature and humidity of the dedicated climate sensor (for example DED\_BLE1\_TEMP and DED\_BLE1\_HUM, or DED\_BLE2\_\* if that sensor is point 2).
* Large ID of the dedicated BLE sensor if you want the MAC in the platform.

Note the position of each field in the small group. The first small field arrives as stt\_small\_assign\_1, the second as stt\_small\_assign\_2, and so on.

On one ST4315B lab object with several small fields selected (voltage, altitude, then BLE measurements), temperature arrived as stt\_small\_assign\_6 and humidity as stt\_small\_assign\_7. If you select fewer fields, or a different order, the slot numbers change. Always confirm the slot in Air Console.



## 2. How Data Reaches Navixy & Platform Setup

Register the tracker with the Navixy model that matches the device.\
<br>

<img src="../../.gitbook/assets/unknown (35).png" alt=" Other ST4300, ST4900, and ST8000 units use their own model names." height="204" width="378">

### How the Tracker Communicates

The tracker transmits three main packet types:

* STT is the periodic status report. It carries mapped BLE measurements on every Fine Tracking interval.
* ALT is the alert report. The tracker sends it when panic, door, or threshold events occur. Mapped BLE fields are included on that packet too.
* BDA is a separate BLE dump. Navixy does not process it. Keep the fields in Report Mapping.

### Platform Setup in Navixy

1. In Navixy, register the device under the device model.
2. Go to Devices > Sensors and buttons.
3. Open Air Console to inspect incoming parameters:
4. Input Bitmask: Read right-to-left. Bit 0 = Ignition, Bit 1 = Input 1, Bit 2 = Input 2. (00000001 = Ignition ON; 00000011 = Ignition ON + Input 1 grounded, using motion ignition).
5. Output Bitmask: Bit 0 = Output 1, Bit 1 = Output 2 (00000011 = both active).
6. Create Measuring Sensors: Map stt\_small\_assign\_6 to Temperature (°C) and stt\_small\_assign\_7 to Custom / Humidity (%). &#x20;

<br>

<img src="../../.gitbook/assets/unknown (38).png" alt=" Navixy Air Console showing incoming stt_small_assign telemetry parameters and hardware states. " height="563" width="212">

### Creating Rules & Alerts in Navixy

Navixy processes hardware events via event\_code and sub\_event\_code:

#### Panic Events (event\_code 136):

* 136\_1: Single click
* 136\_2: Double click
* 136\_3: Triple click

Cooldown Limit: The tracker firmware enforces a 60-second cooldown per click type. A second single click within 60 seconds is dropped, but a double click can still trigger immediately.

#### Door State (event\_code 135):

* 135\_0: Door closed (135;0;6,MAC)
* 135\_1: Door opened (135;1;6,MAC)

#### Sensor Range Alerts:

* 119: Dedicated BLE sensor in range
* 120: Dedicated BLE sensor out of range
* 121 / 122: BLE temperature high / low
* 124 / 125: BLE humidity high / low<br>

**Rule Setup Tip:** Trigger on event\_code if any press or door activity should generate the alert. Trigger on sub\_event\_code (e.g., 135\_1 or 136\_3) when filtering for specific states.

<img src="../../.gitbook/assets/unknown (39).png" alt="Navixy alert rule configuration window using event_code and sub_event_code. " height="487" width="338">

## 3. Quick Verification & Troubleshooting Checklist

1. Panic or door events never arrive? Ensure Continuous scan (4037) is enabled in the BLE Parameter tab.
2. BLE telemetry empty or zero? Verify Parameter 4012 scan time is shorter than the Fine Tracking report time. Ensure mobile setup apps are fully closed.
3. Door state missing? Confirm the door sensor MAC is registered under the Register Door tab, not Register BLE.
