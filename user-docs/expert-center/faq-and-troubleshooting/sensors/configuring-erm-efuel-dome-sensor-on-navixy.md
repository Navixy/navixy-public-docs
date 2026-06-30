---
description: Set up the ERM Efuel Dome fuel sensor in Navixy. Covers physical installation, platform configuration, sensor mapping, and alert setup.
---

# Configuring ERM Efuel Dome sensor in Navixy

This document introduces the **ERM eFuel DOME** sensor, a device engineered for advanced and non-invasive fuel level monitoring across a wide range of vehicles. This innovative sensor provides real-time data analysis, automatic calibration, and intelligent alerts for theft and refueling events. A key technical advantage is its wireless, autonomous, and plug-in design, eliminating the requirement for fuel tank drilling, thereby preserving the vehicle's warranty and structural integrity.

Leveraging the principles of hydrostatics to accurately determine fuel volume based on liquid height, the eFuel DOME sensor offers a distinct advantage over conventional fuel sensing technologies. Once installed externally beneath the fuel tank, it seamlessly transmits data to compatible Starlink devices.

Specifically designed for integration with the Navixy platform via Starlink devices, this solution provides users with comprehensive vehicle insights, extending beyond location tracking to include critical sensor data such as fuel level, height, voltage, pressure, and signal strength.

The subsequent sections of this guide detail the configuration process for the **eFuel DOME sensor, model version 1**, within the Navixy platform.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeIUrW3y6oV9gvGZ10KCKmZ93Rtb62_hEqShhhrhx9hT-xGok9Oz7yg_u2pupFUU92yWiYCfAabambkLtxLFYjs5g176SMkp9PioAGAjes5YbqlbfsgxfGweBtn8CUB1ai2O3w6CQ?key=sL2T3F9AiEzyndTQraY9CGtf)

## **Steps before platform visualization**

Before proceeding with the Navixy platform configuration, it's essential to verify the physical connection between the eFuel DOME sensor and the HUB (antenna) using the **BLE_FuelHub_Monitor configurator tool**, this tool is provided from ERM tech support. Successful communication is indicated by a solid green LED on the HUB. Note that newly delivered sensors from the factory may require initial activation by briefly passing a magnet over them. Once activated, the sensors will establish full communication with the antenna.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdSeFgSutF6XHb3XtDAVmVHmaxuWNycaAnmS7JLgqOfq9EGrbuBzNJZ9lSQTYcyJzWnuru0u1LS4-eWBNl9F_R5JSG9iM88anxoTXIO43Nz4kZPp_Z4bMoQh5Og4nh-sb9PdubKww?key=sL2T3F9AiEzyndTQraY9CGtf)

To achieve the best sensor performance on the platform, it's necessary to properly configure the following parameters:

- Centimeters liquid pressure
- Low fuel level alert #1
- Low fuel level alert #2
- Refuel event minimal difference
- Fuel theft event minimal value
- Ignition ON/OFF report time
- Fuel tank height

Once the sensor is activated and data is successfully displayed within the **Get Diagnostic** tab of the **BLE_FuelHub_Monitor** tool, proceed to fill the vehicle's fuel tank to its full, desired capacity. The corresponding liquid height, measured by the sensor, will be displayed under the **Fuel total centimeters** parameter. **Fuel total centimeters** value is critical as it serves as the 100% reference point for the system's percentage-based fuel level calculations. Therefore, this exact value must be entered into the **Fuel tank height centimeters** field within the **BLE Monitor** tool sensor settings.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeOo40X9hACw9JFzeOTbgIp4dF4RO-0OP-kya9MjPTnshp3pHpguLuAPxxL8NeVlbxvGWLxQOqbg6Bb5tsZ2dN666W2Ppxu_D599laNjx1EjMm2Y-hbHG0A-ObWXP1W0UazA_wivg?key=sL2T3F9AiEzyndTQraY9CGtf)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcc3KbiCeUFtiaCHWDUtoGfvHVJD_eZL4ew_9fIojCkps6DuFZ3SvA339c4nKVIVZJWq4CqgK1SKasBOI_3OTcyDJR6ROddMsCKZ_pL5tzHSqaU1RlDU_QHNrYjQB_moxORHaqfXA?key=sL2T3F9AiEzyndTQraY9CGtf)

With successful sensor activation and data verification via the configurator tool, we can now proceed to map these sensors within the Navixy platform.

## **Device activation on Navixy**

Device activation on Navixy requires the Serial Number located on the device's information label. This serial number, which will range from 4 to 8 digits depending on the specific device model, must be entered as the Device ID during the activation process in Navixy.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdbVfBdrkKyAQEBK86sUqBzaHU6S6vJXJ3dQE8dT8zoP-b5Y2qJUQsDlYOw4ziySWtfBHqINDNnimuwpSNjsvBp3lb7kGjD3gA01eLP_MOfcS0-IwD7v8euJnFQqeI0ZoLVOFPtTg?key=sL2T3F9AiEzyndTQraY9CGtf)

## **Mapping the eFuel sensor on Bluetooth sensor widget**

Once the device is activated and reporting on the platform, go to **Devices and sttings** module, select the device and go to the **Bluetooth sensors** block.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeUQgUo2pAC-CB7Wtno2t_rSo1DxVtlRat8KDuW3IfsWrcPEngo2901tRHP4Qj-8n4wxm3i52_XHQbzRDtIGCJWND-40FS3BB8ANEyizXyliNr3qPFJpIF5Ja9h0z-ZiW4EKmYxYA?key=sL2T3F9AiEzyndTQraY9CGtf)

In this tab, you will need to enter the device's number followed by the sensor's MAC address. This combined information is crucial for the system to correctly decode and interpret the incoming data packages from the eFuel DOME sensor. Ensure accurate entry of both the number and the MAC address to establish proper data processing. Furthermore, it's important to note that the Bluetooth sensor widget within the Navixy platform has the capability to handle data from up to four individual eFuel Dome sensors simultaneously.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfqDY_gSN8NBsjMzyhkMmnP1i_eiHqwUrla4Sy1AbMoL0MntnDnpeIIG3kbRn4I6p85I_Vya5kJcYudsj5oN-hRYo98wn91SCEc8YkOwh-_awhymfXBNJrTqvrb4fEvaoP1ynoQ?key=sL2T3F9AiEzyndTQraY9CGtf)

## **Creating measurement sensors on sensor and buttons widget**

To visualize the data parameters transmitted by the eFuel Dome sensor, a measurement sensor must be created. During this mapping process, it is important to associate each parameter with the corresponding **input number**. For instance, if the first eFuel DOME sensor is identified with index 1, then all data values you wish to retrieve from this specific sensor (such as fuel level, height, voltage, etc.) must be configured to utilize **input 1**. This indexing ensures the platform correctly interprets and displays the data originating from the intended sensor.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcmv_1XAyI8R2cSiXWA5NLsMd85YMd568zN8b18kw7O_Jg5bLA8PnP9HXIQJUKDPcbvgaCmz3_T_NfZisvrrp1C6MhqFc_VXd9ckV3JSjb6B5s_O3HgZpEZtYKryNrXKlrlH615Og?key=sL2T3F9AiEzyndTQraY9CGtf)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf2sFJ9UcwHpXCgpKEyC7QGKCJ5zv5zb_sUFA9VRVn2BaXkdiEfQ4ZymoqXlPf43E5jCNTCjoZGufRb6CxDMNSsw4aSAZciA60NMkII-54FqcdpeaLDMZvBi1m9I-r6TyBnkGEx?key=sL2T3F9AiEzyndTQraY9CGtf)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcxMQJDGhRNqVtf5zi_deayIbObXa-Wpv6n8__jIVDdYK0YkX8oAhcP1o5yOf7SsvUSfWWuWhD06ArTdgL9_B_TSTb-QroHK3NwTKqEbbtHWuypyqnomzzYISy9IBqz2eijHntM?key=sL2T3F9AiEzyndTQraY9CGtf)

The parameters that can be obtained from the sensor are as follows:

- Ble_lls_level_n (Reported fuel value)
- Ble_lls_height_n
- Ble_lls_level_raw_n (Original fuel value)
- Ble_lls_pressure_n
- Ble_signal_strength_n (RSSI)
- Ble_battery_voltage_n

It is important to note that the lls_level and lls_height parameters are inherently included within the data package transmitted by the device at the regular reporting interval. Conversely, other parameters such as voltage and pressure will be obtained with less frequency or specifically requested via the sensor's status command. This deliberate approach optimizes battery life, as transmitting a greater number of parameters more frequently would lead to a faster decrease of the sensor's voltage. Therefore, to ensure longevity and focus on the most critical information for continuous monitoring, parameters beyond the essential real-time fuel level are accessed less frequently.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf0vwMWBiuZrGcfHfv_HCrTMSv0pPfxpJWyhGwPYQD-E7ma_yxOTNuFmkxmNFwaObqONgJNOen0GAV01Lfroxvqtr2exaRZ0yDtsOWUHaUdy4U4kbnvBAxFXTs3WNyD-ucavs1_?key=sL2T3F9AiEzyndTQraY9CGtf)

Events and sub-events from the e-fuel sensor have been integrated following the structure 45_XX, where XX is the sub-event number shown below:

- 155 Fuel theft incident
- 156 Tank filling
- 157 Hub device awakened (after being connected)
- 158 Sensor connected to the Hub
- 159 Sensor disconnected from the Hub
- 160 Sensor missing
- 161 Fuel alert level #1
- 162 Fuel alert level #2
- 163 Incident report (filling/theft)

Go to **Set rules** in the **Alerts** module and look for the Starlink device. Once the Starlink device is selected, choose the **State field value** alert type and enter a name for the alert.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdNRtB36xhNh7uI2DrT_mGtSCkPGAiwKsgIWntnNeX1OveL0qiGaWmuxb4GzrAGjQW2bblFpm0ZbgyaTXKHo50gW44ez6eE2a-nQ-F4ERja0fjGxwUS6Pg2Wwm6GPCGFilesfyj?key=sL2T3F9AiEzyndTQraY9CGtf)

Select the _sub_event_code_ and put the expected value with the sub event of the alert it is required. For instance, if it requires the Efuel theft incident, then the expected sub_event code would be 45_155 as shown below.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfcMLi9LpxSS5e33_3cK_K5O6UeDpYXpUbu9f4Ti9m8zwauO6ANXQQi7O4K063ymZqDrzCELa_5s-IPJijZX0S3DAeYjADomVZzu0sNDRVaiV6EAINh8bZwsfksxroW30UoNAfR9Q?key=sL2T3F9AiEzyndTQraY9CGtf)

Proceed to finish the alert configuration in **Notifications** and **Schedule**.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfbzk0OlN_kYLu7WLhobxp57Ln4_gYqt1MXKeWnK8tdxv82Qfut1KjRA3yIUZTNKI591Doxeks5hOSARKuxQDyblLPknBzLSThwwtxLtmAOV3hl-nZ6xO06vGKcxIx1myqQ17D7Xg?key=sL2T3F9AiEzyndTQraY9CGtf)

With the sensor successfully configured and its parameters mapped within the Navixy platform, the fuel level monitoring process is now complete and ready for real-time observation and analysis through your Navixy interface.

## **Final thoughts**

In conclusion, the ERM eFuel DOME sensor, with its advanced, non-invasive hydrostatic technology and wireless design, offers a significant advantage for accurate and reliable fuel level monitoring across diverse vehicle fleets. Its seamless integration with the Navixy platform via compatible Starlink devices provides a comprehensive solution, delivering not only precise fuel level data but also additional valuable parameters. The ability to map sensor parameters within Navixy and configure alerts empowers users with real-time insights for optimized fuel management, theft prevention, and enhanced operational efficiency. This integration provides a powerful and user-friendly tool for effectively monitoring and managing fuel consumption within any connected fleet.
