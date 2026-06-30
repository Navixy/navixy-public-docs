---
description: Fuel flow meters measure actual consumption using internal chambers. Covers operating principles, vehicle applications, and fluid specifications.
---

# Fuel consumption meters

A fuel flow meter is a device designed to measure the volumetric consumption of fuel, specifically the volume of fuel passing through the fuel line in a given period of time. This essential device presents its measurements in the form of meter readings, similar to those seen on a water or electricity meter. These measurements serve as the basis for calculating important data on a GPS tracking and telematics platform.

* Fuel consumption per time period
* Average fuel consumption, for ex., liters per 100 km/miles per gallon (mpg)

The device not only measures the flow rate of fuels, but also of various liquids and bulk materials. For instance, it can also function as a flow meter for a water meter in an apartment. Therefore, we will use the term "flow meter" to refer general sensor, ensuring clarity and conciseness while maintaining the intended meaning.

## Operating principle

A flow meter is a device used to directly measure the volume of fuel consumption. It features a ring-type measuring chamber, enabling accurate measurements.

![Fuel flow meters - unit structure](../../../../.gitbook/assets/fuel-flow-meter_units.png)

Flow meter operation is primarily founded upon the measurement of fuel volume traversing a designated chamber. This chamber serves as the pivotal component, influencing both accuracy and robustness of the flow meter. Let's explore how all these elements intricately intertwine:

![Fuel flow meter - measurement chamber](../../../../.gitbook/assets/fuel-flow-meter-2.png)

1. The pressure in the flow meter causes the ring to smoothly slide along the inner surface of the measuring chamber, simultaneously sliding along the web. This seamless movement facilitates accurate measurements.

![Fuel flow meter - ring-type measurement chamber](../../../../.gitbook/assets/fuel-flow-meter-3.png)

2. **The ring expels the fluid from inside and outside the chamber through the outlet, directing it towards the outlet nozzle.**

![Fuel flow meter - measuring chamber operating scheme](../../../../.gitbook/assets/fuel-flow-meter-51.png)

3. **One turn of the ring pushes out the volume of fluid equal to the volume of the chamber.** At the same time, the electronic board of the flow meter makes one output impulse. Thus, when fuel passes through a flow meter, an electrical impulse is formed.

![Flow meter electronic board](../../../../.gitbook/assets/fuel-flow-meter-61.png)

The manufacturer's guidelines typically indicate the number of impulses needed for 1 liter or 0.26 US gallons of fuel to pass through the device. Each impulse corresponds to the fuel consumption equivalent to the volume of the measurement chamber. Therefore, the calculation involves determining the difference between the chamber volume and one liter of fuel. This information allows for a precise understanding of the required fuel flow.

For instance, let's consider the Technoton DFM 100D flow meter. In this case, every 1 liter corresponds to 200 pulses. Thus, the measurement chamber volume for this flow meter will be 5 ml, which is 1/200th of a liter. We then input this data as a coefficient for the measuring sensor in the "Sensors and Buttons" section of the telematics platform. Using this coefficient, the impulses are accurately converted into liters. The telematics platform then processes the data received, breaking down the consumed fuel information and presenting it to users in a convenient table format.

## Application

Flow meters are designed to measure fuel consumption in the fuel line of various vehicles and stationary installations. The application area includes:

* **Vehicles.** This can be a railroad, air, or water transport.
* **Additional vehicle equipment.** For example, a compressor unit or other equipment mounted to a vehicle chassis.
* **Stationary installations.** Flow meters can be installed inside the tanks used in small corporate gas stations.

Flow meter readings are not affected by fuel fluctuations in the tank. Therefore, you can unlock the device’s full potential when operating under the following conditions:

* **Constant fuel fluctuations in the tank when driving on a rough terrain.** For example, a flow meter can be used on agricultural machinery (tractors, harvesters, etc.), off-road vehicles or water transport.
* **Fuel tank long-lasting (several hours to several days) inclines.** This usually happens when parking a vehicle on a steep incline. Such situations are typical for a rotary hoe, asphalt milling machines or other types of street maintenance equipment.
* **Fuel fluctuations + long-lasting inclines.** For instance, when working in a quarry with mining trucks.

The specific design of a fuel tank often does not allow to install several [Fuel Level Sensors](../fuel-level-sensors/fuel-level-sensor-installation.md), and using a single fuel level sensor will lead to a higher error rate. In this case, we recommend to opt for flow meters in order to perform a more accurate measurement of the fuel consumed.

[Watch this video](https://www.youtube.com/watch?v=IOCQCNgGG7U), showing flow meter operation on a rough terrain - 2 minutes.

## Working fluids

A flow meter is able to measure any liquid with a [kinematic viscosity](https://en.wikipedia.org/wiki/Viscosity) of 1.5 to 6 mm2/s.

To ensure precise measurements and stable operation of a flow meter, it is crucial to lubricate all units within the measurement chamber. This lubrication applies specifically to diesel fuel consumption measurement, as diesel possesses inherent lubricating properties that minimize friction between surfaces.

In contrast, when it comes to gasoline, the mechanism typically operates "dry," with the exception of two-stroke engines that require a mixture of gasoline and engine oil. This dry operation accelerates wear, reduces measurement accuracy, and introduces additional errors to the flow meter.

{% hint style="info" %}
It is important to note that a flow meter is suitable for measuring diesel fuel consumption exclusively, considering the aforementioned factors.
{% endhint %}
