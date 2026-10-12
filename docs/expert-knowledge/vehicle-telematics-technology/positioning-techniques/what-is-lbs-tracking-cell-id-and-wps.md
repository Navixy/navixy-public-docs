---
description: >-
  How GPS devices find a position from cell towers and Wi-Fi access points, how
  precise LBS positions are, and how Navixy uses them.
tags:
  - fundamentals
---

# LBS positioning with Cell ID and Wi-Fi

LBS positioning finds a device's location from the mobile network and nearby Wi-Fi networks instead of satellites. LBS stands for location-based services. In telematics, the term usually means a position calculated from cell towers, Wi-Fi access points, or both. This page explains how the two methods work, how precise they are, and what Navixy does with these positions. It's for anyone who chooses GPS devices or reads their tracks.

## Why GPS devices use LBS positions

A GPS device uses an LBS position when its satellite receiver can't get a fix. GNSS (global navigation satellite systems, such as GPS, Galileo, GLONASS, and BeiDou) needs a view of the sky. Inside buildings, in underground parking, and in garages, the receiver often sees too few satellites. The cellular modem still connects to the network in these places, and Wi-Fi signals reach indoors.

Power is the second reason. A satellite fix keeps the receiver running for seconds or longer, but the modem already knows which cells are nearby. Battery-powered devices often report LBS positions to run longer between charges.

The cost is precision. An LBS position is usually much less precise than a satellite position, and its error depends on where the device is.

## How Cell ID positioning works

Cell ID positioning places the device at the known location of the cell that serves it. A cellular modem always connects to a cell of a nearby base station and switches cells as the device moves. Each cell has a global identity made of four values:

- MCC: The mobile country code, which identifies the country of the network.
- MNC: The mobile network code, which identifies the operator in that country.
- LAC or TAC: The location area code in 2G and 3G networks, or the tracking area code in LTE and 5G networks.
- Cell ID: The cell's identifier within the network.

A geolocation service looks up these values in a database of cell locations. It returns coordinates and an accuracy radius in meters. Open databases such as [OpenCelliD](https://opencellid.org/) and commercial services collect these locations.

The result is the location of the base station, not the location of the device. Two devices on the same cell get the same coordinates, even when they are far apart. Precision depends on how dense the cells are. In cities, where cells are small, the error is roughly tens to hundreds of meters. In rural areas, one cell can cover a large area, and the error can reach several kilometers. When a device reports several neighboring cells with their signal strength, the service can narrow the estimate.

Mobile networks also have methods that compare signal timing from several base stations, such as E-OTD in GSM networks and OTDOA in UMTS and LTE networks. These methods are more precise, but they need extra measuring equipment in the network and support from the operator. Cell ID needs only the normal network connection, so it is the common choice in GPS devices.

## How Wi-Fi positioning works

Wi-Fi positioning, also called WPS (Wi-Fi positioning system), estimates the location from the access points around the device. The device scans for access points and records the MAC address and signal strength of each one. A geolocation service matches the addresses against a database of known access point locations. It returns coordinates and an accuracy radius.

An access point usually reaches only tens of meters, so a Wi-Fi position is usually more precise than a cell position. Coverage follows buildings. Wi-Fi positioning works well in cities and indoors and gives little or no result on open roads and in the countryside.

The position comes from the signals in one of three ways:

- Signal strength: The service estimates the distance to each access point from its signal strength. Walls and furniture weaken signals, so the estimate varies.
- Fingerprinting: A survey records the signal pattern at known spots in a building, and the device's pattern is matched to the closest recorded spot. The result is precise inside the surveyed area, but a change to the building or the access points requires a new survey.
- Round-trip time: The device measures how long a packet takes to reach an access point and come back. Wi-Fi RTT uses the Fine Timing Measurement feature of the IEEE 802.11 standard and reaches about 1 to 2 meters with three or more supporting access points.

## Why a device reports only LBS positions

A device that sends only LBS positions for a long time has a satellite receiver that can't get a fix. Three causes are common:

- The vehicle stands in a covered place, such as a garage or an underground parking lot.
- The GNSS antenna is disconnected, damaged, or installed where metal blocks the view of the sky.
- The device's GNSS module has failed.

## How Navixy handles cell and Wi-Fi positions

When a GPS device sends a position that it resolved from cells or Wi-Fi, Navixy accepts the position together with the accuracy radius that the device reports. Navixy stores the radius with the position.

Tracking treats a cell or Wi-Fi position like a GPS position. It shows the position on the map and uses it for the asset's motion status and for Trip history. Reports leave cell and Wi-Fi positions out when they build trips.

The accuracy figures on this page describe the technologies in general. The accuracy of a specific position is the radius that the device reports with it.
