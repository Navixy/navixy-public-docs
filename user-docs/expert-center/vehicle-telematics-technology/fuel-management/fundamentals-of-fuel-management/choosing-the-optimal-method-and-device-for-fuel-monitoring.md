---
description: Compare GPS-based fuel estimates, flow meters, and level sensors by accuracy and application to select the right monitoring method for your fleet.
---

# Choosing the optimal method and device for fuel monitoring

In the [previous article](./), we discussed that customer needs are the primary factor in selecting calculation methods and fuel management tools. In this article, we will focus on basic fuel management approaches and calculation methods. In addition, we will review the different types of devices that can be used to collect data and monitor fuel levels, providing a description of their advantages and disadvantages.

First of all, we will analyze approaches that **do not require intervention in the fuel system**. These methods may be useful if you have a limited budget or if manipulation of the fuel system is not acceptable. However, it should be kept in mind that the accuracy of the calculations in this case may be relatively low.

We then will consider approaches with **additional intervention in the fuel system**. In this case, additional equipment is used that integrates into the system and provides more accurate fuel data, but additional sensor and system customizations will be required.

## Fuel management without additional intervention in the fuel system

### Using GPS for fuel control

<table data-header-hidden><thead><tr><th width="209.45458984375"></th><th></th></tr></thead><tbody><tr><td>Uncertainty:</td><td>10-20%</td></tr><tr><td>Benefits:</td><td>- Low cost solution: no additional equipment is required,<br>- No need for installation and customization: no need to install fuel sensors, which simplifies the process of controlling and installing the system.</td></tr><tr><td>Disadvantages:</td><td>- Low calculation accuracy: since the method is based on approximation of fuel consumption data based on coordinates and traveled distance,<br>- Lack of additional data: e.g. fuel level in the tank, which may limit the possibilities for analysis and optimization of fuel use.</td></tr><tr><td>Probability of fraud</td><td>High</td></tr><tr><td>Areas of application</td><td>Fleets where fuel management is not a priority or a 20% error is acceptable.</td></tr></tbody></table>

Fuel management using satellite-based coordinate and distance traveled monitoring without fuel sensors is based on analyzing vehicle movement data and uses satellite systems such as GPS to determine the vehicle's location.

The principle of this method is that:

* By means of satellite monitoring, the coordinates of the vehicle's start and end location are recorded based on GPS data and the distance traveled by the vehicle.
* This data is then used to calculate and monitor fuel consumption based on a set mileage per gallon.

Using fuel consumption information, which can be preset or obtained from other sources, the total fuel consumption is calculated based on the distance traveled.

### Using the OEM sensor via CAN bus/OBDII connector

<table data-header-hidden><thead><tr><th width="208.5455322265625"></th><th></th></tr></thead><tbody><tr><td>Uncertainty:</td><td>10-15%</td></tr><tr><td>Benefits:</td><td>- Real-time information: Because data is received in real time via CAN bus or OBDII connection, operators can get up-to-date information on fuel consumption and monitor fuel utilization in real time<br>- Automation: the method allows automatic collection and analysis of fuel data, which simplifies the process of fuel management and optimization.</td></tr><tr><td>Disadvantages:</td><td>- Manufacturer's Limitations: The capability and accuracy of the data provided by the OEM sensor via CAN bus or OBDII connector may be limited by the vehicle manufacturer. This may limit the functionality and accuracy of the fuel management system.<br>- Difficulty in identifying drains.</td></tr><tr><td>Probability of fraud</td><td>High</td></tr><tr><td>Areas of application</td><td>This method is widely used in fleets where there is a desire to reduce the error in fuel calculations, but it is not possible to install additional equipment in the fuel system.</td></tr></tbody></table>

Fuel management using a standard sensor via CAN bus/OBDII connector is based on the acquisition of mileage, consumption, and fuel level data from the vehicle's on-board network. This system allows you to monitor and optimize fuel usage.

The working principle of this method is as follows:

* Connect to the vehicle CAN bus or OBDII connector, which is a standard connector for accessing vehicle diagnostic data.
* Gain access to mileage, consumption and fuel level data using the OEM sensor, which transmits this data via CAN bus or OBDII connector.
* Analyze the resulting data to monitor fuel usage and determine vehicle efficiency.
* Identification of anomalies or abnormal situations such as fuel losses due to leaks or inefficient use.

### Using OEM sensor and connecting to the analog input

<table data-header-hidden><thead><tr><th width="209.45458984375"></th><th></th></tr></thead><tbody><tr><td>Uncertainty:</td><td>10-15%</td></tr><tr><td>Benefits:</td><td>- Integration: connecting the OEM sensor to the analog input allows integration with the vehicle's control system or other fuel monitoring and management devices.<br>- Easy to install: OEM fuel sensors are usually already installed in vehicles, so their use does not require significant modifications to the design or the installation of additional devices.</td></tr><tr><td>Disadvantages:</td><td>- Occurrence of errors: OEM fuel gauges lack internal data processing algorithms, which can lead to short-term deviations in readings. Errors can also occur due to voltage surges in the vehicle's onboard network.<br>- Not possible to keep the warranty on the car because the GPS tracker is connected to the car's wiring, which requires a qualified employee who will be able to make the connection.<br>- Probability of manipulation: since the method is based on data from the OEM sensor, it is possible to manipulate the data to distort information about the actual fuel level.</td></tr><tr><td>Probability of fraud</td><td>High</td></tr><tr><td>Areas of application</td><td>This method is used in vehicles (mainly cars and trucks) to monitor fuel levels and detect changes in fuel levels. It allows drivers and vehicle management systems to determine with average accuracy also the remaining fuel reserve and to plan journeys or trips with this data in mind.</td></tr></tbody></table>

Fuel control using the OEM sensor and connection to the analog input is performed by connecting the fuel level sensor to the analog input of the GPS tracker. This allows you to control the fuel level and consumption.

The working principle of this method is as follows:

* To connect the OEM fuel sensor to an analog input, a wired connection between the two devices must be used. As a rule, different types of interfaces and connectors are used depending on the vehicle manufacturer and model.
* After connecting the sensor to the analog input, the system must be calibrated and configured. This will provide a more accurate reading of the fuel level data.
* The resulting fuel level data can be used for a variety of purposes. For example, you can monitor the fuel level in real time, determine the fuel consumption on a certain section of the journey, or warn the driver of low fuel levels.

It is important to note that the process of controlling fuel using the OEM sensor and connecting to the analog input may vary depending on the specific vehicle model or control system. It is recommended that you consult your owner's manual or the vehicle manufacturer for exact instructions and recommendations.

## Fuel management with additional equipment integrated into the fuel system

### Using a flow meter (fuel flow sensor)

<table data-header-hidden><thead><tr><th width="208.5455322265625"></th><th></th></tr></thead><tbody><tr><td>Uncertainty:</td><td>1-3%</td></tr><tr><td>Benefits:</td><td>- High data accuracy: the data error for flow meters can be less than 3%,<br>- Versatility: the flow meter can be installed on all practically all types of vehicles and stationary objects.</td></tr><tr><td>Disadvantages:</td><td>- Cost: some flow meters can be quite expensive, especially if they need to be installed and integrated into complex systems,<br>- Difficulty of installation and customization: installation of the flow meter involves incorporation into the fuel system, so it must be done by qualified specialists,<br>- Data limitations: the use of a flow meter does not allow for real-time tracking of drains,<br>- Exposure to failure: Like any technical device, flow meters can be subject to malfunctions and breakdowns requiring repair or replacement.</td></tr><tr><td>Probability of fraud</td><td>Medium</td></tr><tr><td>Areas of application</td><td>It can be used in fleets and facilities that are not suitable for installation of fuel flow meters, but where high-quality information on fuel consumption is required.</td></tr></tbody></table>

The principle of operation of a flow meter is to measure the volume of fuel flowing through it. Flow meters can use different technologies to determine the fuel flow rate and are therefore of different types - mostly mechanical single-chamber sensors are used.

The principle operation method of using a flow meter mounted in the fuel line consists of the following steps:

1. Selecting the installation location: Before installation, the optimum location for the flow meter must be selected. It must be accessible and safe for personnel.
2. Pipeline Preparation: The fuel line must be accessed by shutting off the fuel supply and reducing the pressure to a safe level. Then disconnect the connecting flanges or fittings to create a place for the flow meter installation.
3. Flow meter Installation: The flow meter must be properly oriented and installed on the pipeline using connecting flanges or fittings. Please note that the manufacturer's instructions must be followed during installation and the connections must be sealed.
4. Connecting the cables: After the flow meter is installed, the data cables must be connected. The cables can be laid in protective ducts or pipes to ensure safe and reliable communication.
5. Check and Setup: After installation of the flow meter, check its operation and adjust the parameters according to the requirements of the monitoring or control system. It is recommended to perform a test run to verify the accuracy of measurements and correct data transmission.

It is important to note that the flow meter installation process may vary depending on the specific model and type of fuel system.

The fuel consumption data received is processed by an electronic sensor, which converts it into a digital signal and transmits it to the instrument panel or control system, where the information can be displayed or used for analysis and control.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="267"><figcaption><p>Fuel flow meter</p></figcaption></figure>

### Using a fuel level sensor

<table data-header-hidden><thead><tr><th width="209.4544677734375"></th><th></th></tr></thead><tbody><tr><td>Uncertainty:</td><td>1-3%</td></tr><tr><td>Benefits:</td><td>- High data accuracy: the data error for digital RTDs can be less than 1%, provided that installation and configuration requirements and recommendations are followed,<br>- An additional set of fuel related parameters that help control fuel, such as fuel temperatures,<br>- Versatility: Fuel level sensors can be installed on all types of vehicles, including non-standard tank shapes.</td></tr><tr><td>Disadvantages:</td><td>- Difficulty of installation and customization: installation of the RTD should be carried out strictly in accordance with the manufacturer's requirements and recommendations by qualified specialists,<br>- Regular maintenance: Using the fuel level sensor as a fuel data source implies maintenance. It is recommended to check and tare once a year.</td></tr><tr><td>Probability of fraud</td><td>Low</td></tr><tr><td>Areas of application</td><td>It is used everywhere in all fleets and facilities that have tank(s), and in various shapes. It is also used in the marine industry, special equipment, generators and stationary engines.</td></tr></tbody></table>

A fuel level sensor is a device that is used to measure and monitor the fuel level in a vehicle tank or reservoir. It plays a key role in managing and monitoring the fuel supply.

The working principle of a fuel level sensor is usually based on the use of various technologies such as:

1. Capacitive sensor: Capacitive sensors are used to measure the change in capacitance of electrodes immersed in fuel. The fuel level affects the electrode capacitance to determine the fuel level.
2. Ultrasonic sensor: Ultrasonic sensors are used to measure the time taken for ultrasonic waves to reflect off the fuel surface. The time measurement allows the distance and therefore the fuel level to be determined.
3. Resistive sensor: Resistive sensors work on the basis of resistance changes in relation to the fuel level. The changing resistance is transmitted as a signal indicating the fuel level.

These are just some of the types of fuel level sensors that are used in various vehicles and equipment. The specific sensor type may vary depending on the application and the required measurement accuracy. More information on sensor types and technologies can be obtained from our academy.

It should be noted that there are wired and wireless fuel level sensors. Wireless fuel level sensors use Bluetooth © to connect to the tracker, which can be an advantage over wired ones due to the absence of the need for additional work, including wiring.

The installation of the sensor is an important part of the fuel level sensor. The fuel level sensor is installed in the vehicle's fuel tank. It is usually mounted on the top of the tank. As mentioned earlier - we strongly recommend to use the services of professional installers, because even a vertical deviation of a few degrees or installation in the wrong place can give additional error in measurements. Taring is also an important process. It is necessary to make sure that taring is done according to the manufacturer's instructions and with a sufficient sample of spills (at least 20).

Additionally, it should be noted that for vehicles with non-standard fuel system, e.g. one tank of non-standard shape or two tanks (main and auxiliary) two or more sensors are required.

{% columns %}
{% column %}
<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>Digital fuel level sensor</p></figcaption></figure>
{% endcolumn %}

{% column %}
<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption><p>Analog fuel level sensor</p></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

## Conclusion

After reviewing the articles on selecting a method and equipment for fuel control, the following conclusion can be drawn. When selecting a fuel monitoring method, the goals, objectives, and budget should be considered. There are various methods and equipment for monitoring fuel consumption such as fuel level gauges, OEM gauges, electronic sensors, and GPS monitoring. Each of them has its advantages and disadvantages, but one should always keep in mind the potential error in calculations, which ranges from a high of 15-20% for standard tools and systems without additional equipment to the lowest of 1-3% for facilities that use additional equipment built into the fuel system, which requires additional settings, including taring - an integral step in the initial setup of the system for fuel calculation. The decision to select a method and equipment for fuel control should be informed and tailored to your needs.
