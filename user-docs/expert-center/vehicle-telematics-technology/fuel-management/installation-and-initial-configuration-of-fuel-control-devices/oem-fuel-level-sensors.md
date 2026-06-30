---
description: Factory-fitted level sensors measure tank fuel through resistance variation and transmit data via CAN/OBD-II or analog inputs to the GPS device.
---

# OEM fuel level sensors

Original Equipment Manufacturer (OEM) fuel level sensors are installed by manufacturers during vehicle production. These sensors are built to accurately gauge the fuel levels within the tank and seamlessly relay this crucial information to the vehicle's dashboard and other vital systems.

Standard (OEM) fuel level sensors operate on a principle that relies on the variation of electrical resistance in relation to the fuel level in the tank. These sensors are equipped with a floating element that ascends and descends with the fuel level. As the fuel level fluctuates, so does the resistance, consequently altering the sensor's output signal.

## Data acquisition via CAN / OBDII

CAN (Controller Area Network) and OBDII (On-Board Diagnostics 2) are two different protocols used in the automotive industry to exchange information and diagnose vehicles.

- CAN is a standard data bus that enables communication between various components and devices of a vehicle. It is used to transfer data between different systems such as engine, transmission, safety systems, and more. CAN provides high data transfer speed and fault tolerance, making it ideal for use in automobiles.
- OBDII, on the other hand, is a vehicle diagnostic standard that uses communication protocols, including CAN, to exchange information between the vehicle computer and external diagnostic tools. OBDII provides access to various vehicle parameters such as engine speed, coolant temperature, fuel level and more.

To get fuel data using CAN and OBDII, you need a GPS device that supports these protocols. The choice of such GPS devices is quite large. You can see the list of more than a thousand devices supported by the Navixy platform to select GPS devices based on your needs at the following link: [https://www.navixy.com/devices/](https://www.navixy.com/devices/) .&#x20;

It should be noted that CAN-based GPS devices transmit more telemetry information that can be used for complex and detailed fleet management scenarios. On the other hand, OBDII devices have a more convenient installation process. Let's take a look at the simplest option of connecting the device via the OBDII connector. For this, you need to follow the steps below:

1. Find the OBDII connector in your vehicle. It is usually located under the steering wheel or near the instrument panel.
2. Connect the GPS device to the OBDII connector. Make sure that the GPS device contains a SIM card for telematics data transmission and that the pins are well connected and their positions match.
3. Turn on the vehicle and check if the GPS device is working. Wait a while for the device to initialize the connection. It should start receiving data on fuel and other vehicle parameters and transmit them over the Internet.
4. Check the data you receive from the GPS device in your Navixy account. (Remember to activate the GPS device before use)

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>OBD II connection</p></figcaption></figure>

## Connecting the device to analog input

In some cases you can connect the GPS device to the analog input of the OEM sensor. This process is much more complicated, because, firstly, most modern GPS devices usually use digital interfaces for data transmission, so you need to make sure that the device has the ability to work with analog data. Secondly, direct connection requires additional work associated with wiring additional cables. However, if you have a specific GPS device with analog output, the following steps can help you connect it to analog input:

1. Make sure the GPS device has an analog output that can be connected to the analog input of your device or system.&#x20;
2. Find the cables connecting the OEM fuel level sensor terminal.
3. Using an appropriate cable or adapter, connect the analog output of the GPS device to the analog input of your device or system. Make sure you have compatible connectors.
4. If necessary, adjust the input settings on your device or system so that it properly recognizes and processes the signal from the GPS device, including taring.
5. Check the connection and settings to make sure that the data from the GPS device is correctly transmitted and processed by your device or system.

It is important to note that specific instructions for connecting your GPS device to the analog input may vary depending on the make and model of your device or system. It is recommended that you see the documentation or user manual of your device or system, and consult the GPS device manufacturer for exact instructions and recommendations.
