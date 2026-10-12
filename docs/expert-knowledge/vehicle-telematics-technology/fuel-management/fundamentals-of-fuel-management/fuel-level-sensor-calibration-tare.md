---
description: >-
  How sensor calibration and tank calibration link a fuel level sensor's
  reading to the fuel volume, and why calibration decides fuel data accuracy.
tags:
  - fundamentals
---

# Fuel level sensor calibration (tare)

Calibration, also called taring, links a fuel level sensor's reading to the volume of fuel in the tank. A level sensor measures how high the fuel stands, not how much fuel there is. Most tanks don't hold the same volume at every height, so a reading means nothing in liters until the sensor and the tank are calibrated. The quality of the calibration limits the accuracy of all fuel data that comes after it.

This page is for installers and fleet managers who plan a fuel level sensor installation. It describes the two calibration stages, what makes a calibration accurate, and where the conversion to liters happens. The sensor manufacturer's procedure is the authority for each sensor model. Where the manufacturer's guide differs from the general practice described here, follow the manufacturer's guide.

## Where the conversion to liters happens

Navixy stores fuel level values as the device sends them. The Navixy platform has no calibration table of its own, so a conversion that follows a calibration table happens in the sensor or in the GPS device.

Some fuel level sensors report the level in their own counts, not in liters. A count is a number on the sensor's own scale, and the `lls_level` value is an example. If neither the sensor nor the device converts the counts, the values that Navixy stores are counts too. To get liters, load the calibration table into the sensor or the device, with the tools that the manufacturer provides.

When one coefficient is enough to convert counts to liters, an IoT Logic flow can do the conversion instead. Its [Initiate Attribute](https://app.gitbook.com/s/6RLA9o0EecKpLxHiaeR4/products/iot-logic/nodes/initiate-attribute) node calculates the liters from the count, for example `lls_level * 0.1`, and adds the value to the message next to the original reading. A tank whose shape needs a calibration table with many points still needs the table in the sensor or the device.

Because Navixy uses the level values without changes, an error in the calibration stays in the fuel data.

## Sensor calibration

Sensor calibration sets the sensor's own measuring range before the sensor goes into the tank. It applies to sensors with a measuring probe, such as capacitive sensors. An installer usually cuts the probe to match the tank depth. After the cut, the sensor needs a new empty point and a new full point.

The installer records both points with the manufacturer's configuration software on a laptop:

* Empty point: The probe is dry, and the fuel has fully drained from it.
* Full point: The probe is in fuel along its whole length, and the reading is stable.

For the full point, installers use a calibration tube. A calibration tube is a plastic pipe that is closed and sealed at one end. A typical tube is about 700 mm long, with an inner diameter of about 50 mm. The tube must be longer than the cut probe. The installer fills the tube with the same fuel that the tank holds, because the type of fuel affects a capacitive sensor's reading. The [Fuel level sensor installation](../installation-and-initial-configuration-of-fuel-control-devices/fuel-level-sensors/fuel-level-sensor-installation.md) page lists the tube as part of the calibration set.

## Tank calibration

Tank calibration builds a calibration table for one tank. Each table entry pairs a sensor reading with the fuel volume that matches it. The installer starts with an empty tank and adds fuel in measured, equal portions until the tank is full. After each portion, the installer waits until the reading settles, then writes down the reading and the total volume in the tank.

### Number of entries and portion size

A calibration table needs at least 20 entries, one for each portion. Each portion is then one twentieth of the tank volume or less. For a 400-liter tank, a portion is 20 liters or less. Smaller portions give more entries and a more accurate table.

Where the tank shape changes, such as at a narrowing, a widening, or a step, the installer uses portions of half the size. The extra entries let the table follow the shape of the tank. Without them, the table assumes a straight line between two entries that are far apart, and the fuel values in that part of the tank are wrong.

### Measuring each portion

Each portion is measured with a measuring container of certified volume or with a fuel flowmeter. A fuel dispenser with a meter, such as a portable fuel station, works as a flowmeter. Every table entry uses the total volume poured so far. An error in one portion therefore moves every entry after it.

The vehicle stays on level ground for the whole calibration. When the vehicle tilts, the fuel height at the sensor changes for the same volume.

### When to repeat the calibration

A calibration table is valid for one tank and one sensor position. Repeat the tank calibration when the sensor is moved or replaced, when the probe is cut again, or when the tank is repaired or replaced. Also repeat it once a year, as the [Fundamentals of fuel management](README.md#tare-calibration) page recommends.

## See also

* [Fundamentals of fuel management](README.md): Learn which factors decide the accuracy of fuel data.
* [Fuel level sensor installation](../installation-and-initial-configuration-of-fuel-control-devices/fuel-level-sensors/fuel-level-sensor-installation.md): Learn which tools an installation takes and where calibration fits in it.
