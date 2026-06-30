---
description: Single-chamber, dual-chamber, and CAN-bus fuel meters each suit different engine types. Compare specifications to select the right meter for your vehicle.
---

# Types of fuel consumption meters

Flow meters manufactured by [Technoton](https://www.jv-technoton.com/) are one of the most widely-used sensors. In our guide, we will classify flow meters as per Technoton’s listing. Other manufacturers produce similar models of flow meters as well but may use their own naming scheme. For example, the [Mechatronics](http://mechatronics.by/) manufacturer company refers to flow meters as "fuel consumption sensors".

For a comprehensive grasp of different flow meters, it is recommended to start by examining the engine fuel system. This will provide valuable insights into understanding the various types available and how they function.

![Fuel flow meter](../../../../.gitbook/assets/fuel-flow-meter-71.png)

1. Fuel tank
2. Rough filter
3. Low-pressure fuel pump
4. Fine filter
5. High-pressure fuel pump
6. Injectors
7. Bypass valve

On the scheme you can differentiate:

- **Supply fuel line** (marked by orange and red arrows) that delivers fuel from the tank to the “engine” (to be more precise, to the high-pressure fuel pump and injectors).
- **Return fuel line** (marked by blue arrows) that removes excess fuel from the “engine” (from the high-pressure fuel pump and injections) and transfers it to the tank.

The low-pressure fuel pump supplies a surplus of fuel to the high-pressure fuel pump, surpassing the engine's requirements in all operational modes. Any excess fuel from the high-pressure pump and injectors is directed back to the fuel tank through the return line.

As an example, let's consider the idle period readings for a John Deere tractor: the supply fuel line records a flow rate of 200 liters per hour, while the return line captures 197 liters per hour. The engine's fuel consumption during this time will be 3 liters per hour.

## Single-chamber flow meter

Single-chamber flow meter measures the amount of fuel flowing through the fuel supply line, that is, from the fuel tank to the “engine”.

![Single-chamber flow meter](../../../../.gitbook/assets/fuel-flow-meter-81.png)

Single-chamber flow meters are divided into the following types:

- **Autonomous flow meter** - its measurement results are displayed on the upper part of the flow meter. Such devices are powered by a built-in battery and are best used to control unauthorized fuel draining on stationary tanks, etc.
- **With interface cable** - such meters are used to transfer data to a monitoring platform. To do this, it is enough to connect to the GPS tracker using one of the following interfaces:
  - Impulse interface
  - RS-232
  - RS-485
  - CAN

Flow meters with an interface cable derive electrical power from the onboard source of a vehicle. In the event of a power outage, the device seamlessly transitions to its built-in battery, ensuring uninterrupted fuel consumption data storage. Once power is restored, the accumulated data is seamlessly transferred from the internal memory to the monitoring platform.

The meter's operational status can be easily determined by observing the LED indicator located on the top. A correct functioning device is indicated by a flashing LED.

Alternatively, a flow meter with an interface cable can be equipped with a display to showcase measurement results in real-time.

## Dual-chamber flow meter

A flow meter that features two measurement chambers is commonly referred to as "dual-chamber" or "differential". These types of fuel flow meters accurately measure fuel consumption by quantifying the difference in the volume of fuel flowing through the supply and return fuel lines. In other words, it calculates the amount of fuel delivered from the tank to the engine while subtracting the fuel volume returning from the engine to the tank. This dual-chamber design ensures precise measurement and monitoring of fuel usage.

![Differential fuel flow meter](../../../../.gitbook/assets/fuel-flow-meter-91.png)

Differential flow meters are:

- **Autonomous flow meter** - its measurement results are displayed on the upper part of the flow meter. Such devices are powered by a built-in battery and used for fuel monitoring in vehicles (data transmission to a monitoring platform is not allowed).
- **With interface cable** - it can transfer data to a monitoring platform. To do this, connect to a GPS tracker with one of the following interfaces:
  - Impulse interface
  - RS-232
  - RS-485
  - CAN

Differential flow meters connected through an interface cable are powered by the vehicle's onboard power source. In the event of a power outage, the meter seamlessly switches to its built-in battery, preserving fuel consumption data in the backup memory. Upon power restoration, all accumulated data is transferred from the internal memory to the monitoring platform.

To determine the operational status of the differential flow meter, observe the LED indicator located on the upper part. A flashing indicator signifies proper device functionality.

For added convenience, a differential flow meter with an interface cable can be equipped with a display screen to showcase measurement results.

## CAN-bus flow meter

Automakers have the potential to transmit fuel consumption data through a network of vehicles or specialized equipment known as a CAN-bus. Instead of relying on physical devices, a CAN-bus flow meter utilizes an algorithm that calculates fuel consumption by considering the operating time of injectors. This time is then multiplied by the rate at which fuel flows through the injector. To ensure accuracy, other CAN parameters are taken into account during the adjustment process. As a result, precise fuel consumption data for both diesel and gasoline vehicles can be obtained.

**In a monitoring and telematics platform**, fuel consumption data is represented by a digital meter that keeps track of the amount of fuel consumed in liters or gallons throughout a vehicle's operation. To determine the daily fuel consumption, you simply need to subtract the meter readings recorded at the beginning and end of each day and take into account any refills. Using this principle, the Flow Meter report is generated, providing insights on fuel consumption. You can explore the Reports section to see how it presents this information.

You can find out whether fuel consumption data can be transmitted via a CAN-bus of your vehicle by switching to this table of [CAN parameters](https://www.navixy.com/wp-content/uploads/2019/07/allcan300_list.pdf). Choose your car model and check if the “Total fuel consumption” parameter is marked as +.

As for the devices produced by third-party manufacturers, for example, [Nozzle Crocodile](https://www.jv-technoton.com/ru/produkty/nozzle-crocodile/), they can also count fuel consumption based on the injectors operation time but have lower accuracy in comparison with the standard CAN flow meter. As a rule, such devices are used to control fuel consumption in case other methods are not available. For example, one can use them for measuring gas consumption on LPG (Autogas) vehicles.

![Contactless NozzleCrocodile reader, manufactured by Technoton.](../../../../.gitbook/assets/fuel-flow-meter-10-11.png)

## Flow meter selection

What should be considered includes:

- **Engine power**: this parameter influences the minimum and the maximum engine fuel consumption (liters per hour). Manufacturers of flow meters specify two separate parameters corresponding to fuel consumption:
  - **Maximum flow rate**: shows the maximum amount of fuel that can pass through the measured chamber of the flow meter (liters per hour). The maximum flow rate must be greater than the maximum engine fuel consumption value.
  - **Minimum flow rate**: indicates the minimum amount of fuel required for a measured chamber to start operating. The minimum flow rate must be less than the minimum fuel consumption value of the engine.In case a fuel consumption value is greater than the max flow rate, **the engine will not receive the required amount of fuel** and will not reach its full power and may become unstable or stall out.

{% hint style="info" %}
Bear in mind that in some situations (for example, when a vehicle idles or runs at a low speed), a fuel consumption value may be less than the min flow rate. **This data will not be taken into account** by the flow meter and will lead to lower accuracy.
{% endhint %}

Flow meter selection depending on the engine power:

| Engine power, kW | Engine power, hp | Maximum flow rate, l/h | Minimum flow rate, l/h | Examples of vehicles and machinery              | Recommended flow meters by Technoton |
| ---------------- | ---------------- | ---------------------- | ---------------------- | ----------------------------------------------- | ------------------------------------ |
| 0-80             | 0-109            | 50                     | 1                      | Small tractor for housing and communal services | DFM 50                               |
| 80-150           | 109-204          | 100                    | 2                      | Rotary hoe                                      | DFM 100, DFM 100D                    |
| 150-300          | 204-408          | 250                    | 5                      | Combined harvester, wheeled all-terrain vehicle | DFM 250, DFM 250D                    |
| 300-600          | 408-816          | 500                    | 10                     | Dump truck                                      | DFM 500, DFM 500D                    |

In the absence of data on engine power, we recommend to search for fuel consumption value for each transport category separately.

- **Whether you have a car or special equipment under warranty**, it is better to use differential flow meters. They work well and the installation process does not require making changes to the fuel system.
- **Connection interfaces**: the following types of interfaces are used to connect flow meters to GPS trackers:
  - Impulse inputs
  - Serial interfaces:
    - RS-232
    - RS-485
    - CAN-likeThe interface for connecting a flow meter to a GPS tracker is selected when ordering the meter from a manufacturer. **Note that a flow meter can have only one interface and it should correspond with that of the tracker.** Hence, before ordering it is worth checking what interfaces are available on your GPS tracking unit.

## Leading flow meters manufacturers

Technoton (Belarus)

- **Marketed in:** Eastern Europe, Latin America, CIS, Middle East.
- Available languages: English, Spanish [https://www.jv-technoton.com](https://www.jv-technoton.com/)
- [LinkedIn](https://www.linkedin.com/company/technoton/), [Twitter](https://twitter.com/JV_Technoton)

|                                                                                         |           |                                                                                                                                                                                                                                                                                                                               |
| --------------------------------------------------------------------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Photo**                                                                               | **Model** | **Description**                                                                                                                                                                                                                                                                                                               |
| ![Fuel flow meter tutorial](../../../../.gitbook/assets/fuel-flow-meter-25-150x150.png) | DFM       | <p>Single-chamber flow meter<br><br>ТТХ: <a href="https://www.navixy.com/wp-content/uploads/2019/07/parameters.pdf">EN</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/parametr.pdf">RU</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/caracter-sticas-t-cnicas.pdf">ES</a></p>            |
| ![Fuel flow meter tutorial](../../../../.gitbook/assets/fuel-flow-meter-26-150x150.png) | DFM D     | <p>Dual-chamber/Differential flow meter<br><br>ТТХ: <a href="https://www.navixy.com/wp-content/uploads/2019/07/parameters.pdf">EN</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/parametr.pdf">RU</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/caracter-sticas-t-cnicas.pdf">ES</a></p> |

Mechatronika (Belarus)

- **Marketed in:** Europe, Latin America, CIS
- Available languages: English, Spanish, French, Portuguese, Polish [http://mechatronics.by/](http://mechatronics.by/)
- [FB](https://www.facebook.com/mexatronika/)

|                                                                                 |                 |                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Photo**                                                                       | **Model**       | **Description**                                                                                                                                                                                                                                                                                                                                          |
| ![Fuel flow meter tutorial](../../../../.gitbook/assets/fuel-flow-meter-27.png) | Eurosens Direct | <p>Single-chamber flow meter<br><br>ТТХ: <a href="https://www.navixy.com/wp-content/uploads/2019/07/parameters-eurosens.pdf">EN</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/parametr-eurosens.pdf">RU</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/caracter-sticas-t-cnicas-eurosens.pdf">ES</a></p>            |
| ![Fuel flow meter tutorial](../../../../.gitbook/assets/fuel-flow-meter-28.png) | Eurosens Delta  | <p>Dual-chamber/Differential flow meter<br><br>ТТХ: <a href="https://www.navixy.com/wp-content/uploads/2019/07/parameters-eurosens.pdf">EN</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/parametr-eurosens.pdf">RU</a> / <a href="https://www.navixy.com/wp-content/uploads/2019/07/caracter-sticas-t-cnicas-eurosens.pdf">ES</a></p> |
