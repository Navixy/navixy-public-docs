---
description: Configure Teltonika BLE beacons in Navixy as temperature, magnet detection, or driver ID sensors. Works without wiring, with battery life up to 19 years.
---

# BLE beacons (Teltonika)

Teltonika BLE beacons are versatile devices that can collect data on temperature, magnetic state, and transmit their identifiers over long distances, reaching up to 200 meters in open areas.

<figure><img src="https://www.navixy.com/wp-content/uploads/2020/10/bluetelt.png" alt="" width="375"><figcaption></figcaption></figure>

These beacons use **Bluetooth Low Energy** technology (BLE 4.0+, a short-range wireless standard optimized for low power consumption), allowing them to run for extended periods on their internal batteries: up to 2 years for SLIM (card-sized) models, up to 5 years for COIN (small disc) models, and up to 19 years for PUCK (large rugged) models.

The beacons are housed in IP68-rated cases (sealed against dust and safe to submerge in water).

## Supported beacon models

* **Blue COIN MAG**: Small magnetic beacon
* **Blue PUCK MAG**: Large magnetic beacon
* **Blue COIN ID**: Small ID beacon
* **Blue PUCK ID**: Large ID beacon
* **Blue SLIM ID**: Card-sized beacon for employee identification
* **Blue COIN T**: Small temperature beacon
* **Blue PUCK T**: Large temperature beacon

### Temperature sensors

To add a temperature sensor:

1. Navigate to the measuring sensors section in the input tab.
2. Select **BLE: Temperature** as the input type.
3. Choose **Temperature** as the sensor type.

<figure><img src="https://www.navixy.com/wp-content/uploads/2020/10/chrome_zqbp0ajule.png" alt=""><figcaption></figcaption></figure>

### Magnetic sensors

Magnetic (MAG) beacons detect whether a magnet is present or absent, useful for monitoring container doors, toolbox lids, or other assets. Configure them as a virtual sensor using the **Source value** method:

1. Navigate to **Sensors and buttons** and click **+** → **Virtual sensor**.
2. Select the **BLE: Magnetic field** input that corresponds to the beacon's slot number.
3. Choose **Source value** as the calculation method.
4. Map the two states (for example, `0` → `Open`, `1` → `Closed`) and save.

The sensor status appears in the **Sensor readings** block of the [Object widget](../../../../tracking/objects-list/object-widget.md).

### Driver identification sensors

ID beacons (COIN ID, PUCK ID, SLIM ID) work like iButton or RFID but over Bluetooth. No physical reader required. When a beacon comes within range of the GPS device, its identifier is read automatically. Configure them as a virtual sensor using the **Source value** method:

1. Navigate to **Sensors and buttons** and click **+** → **Virtual sensor**.
2. Select the **BLE: Hardware key** input.
3. Choose **Source value** as the calculation method.
4. Map each beacon's MAC address or identifier to a driver name and save.

Once configured, the identified driver appears in the **Sensor readings** block and can be used in driver identification rules.

**See also:**

* [Assigning driver ID keys in the Driver’s profile](../../../../fleet-management/drivers.md)
* [Driver identification (Rules and notifications)](../../../../events-and-notifications/scheduling-and-dispatching/driver-identification.md)
