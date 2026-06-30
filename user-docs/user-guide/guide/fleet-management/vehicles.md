---
description: Add fleet vehicle profiles with specs, fuel consumption rates, and insurance data. Link vehicles to GPS devices and import multiple profiles from Excel files.
---

# Vehicles

Vehicles in Navixy are essential for tracking and managing your fleet. They allow you to monitor various aspects such as location, fuel consumption, maintenance schedules, and overall fleet performance, enabling efficient operations and better decision-making.

## Vehicles tab

The **Vehicles** tab presents a detailed overview of all vehicles within your fleet. The information is organized in a table format, complemented by a visual menu on the right side of the screen. Here, you can easily add or edit vehicle details, associate them with specific depots, and link them to GPS devices that have been activated on the Navixy platform.

### Adding a new vehicle

To add a new vehicle, click the **+** button. You also have the option to upload an image of the vehicle for easy identification.

* **Main tab:** Enter essential vehicle information, including tags and any relevant notes.
* **Specification tab:** Provide detailed specifications, such as vehicle dimensions, wheelbase size, the number of wheels, permitted speed, trailer availability, and the year of manufacture.
* **Fuel tab:** Record fuel-related information, including fuel type, tank capacity, and consumption rate per 100 km (or miles). This data is crucial for generating accurate fuel consumption reports.
* **Insurance tab:** Input the vehicle’s insurance details, including the policy number and expiration date.

<details>

<summary>Fuel consumption in the Vehicle profile and its role in Fuel reports</summary>

In Navixy, configuring the **Fuel consumption** in the vehicle profile is a critical step for roughly tracking and reporting fuel usage across your fleet based on mileage driven, without relying on OBDII data or specialized fuel sensors.

This parameter is typically defined in terms of liters per 100 kilometers (L/100 km) or miles per gallon (MPG), depending on your regional preference.

#### How fuel consumption is used in fuel reports

1. **Estimation of fuel usage:**\
   The fuel consumption value entered in the vehicle profile serves as a baseline for estimating the fuel usage of a vehicle over a given distance. For instance, if a vehicle is set to consume 10 L/100 km, the Navixy platform estimates that it uses 10 liters of fuel for every 100 kilometers driven.
2. **Calculation of expected Fuel costs:**\
   Navixy uses the set fuel consumption rate along with the recorded mileage to calculate the expected fuel costs. By inputting the price per liter or gallon in the settings, the Navixy platform can generate reports that estimate how much you should be spending on fuel, which helps in budgeting and financial planning.
3. **Comparison with actual fuel data:**\
   When combined with data from fuel level sensors, Navixy can compare the estimated fuel consumption with actual fuel usage. This comparison helps in identifying discrepancies, such as fuel theft, inefficiencies in driving behavior, or issues with the vehicle's engine that might lead to higher-than-expected fuel consumption.

</details>

### Vehicle import

If you have a large fleet and need to create profiles for multiple vehicles, it's more efficient to import all the information at once using a single file, rather than creating vehicle profiles one by one. The data should be in XLS, XLSX, or CSV file formats.

To import vehicle profiles:

1. Open the **Fleet management** application, click **Add vehicle**, and select **Import from Excel file**.
2. In the import window, you'll find an example Excel file that you can use as a template.
3. Ensure that the columns in your file correspond to the correct fields in the tracking system by entering the appropriate header fields. This can be done either before the import or during the process.
4. In the loaded file, you’ll need to specify key information such as:
   1. Name
   2. License Plate
   3. Type\
      \
      After completing the form, save the file on your computer.

To upload the file into the Navixy platform:

1. Click the **Select** button, find your file, and click **Next**.
2. You'll be prompted to review the header fields. If everything is correct, click **Next** again.
3. If any fields are incorrect, the Navixy platform asks you to correct them. If any required fields are empty, that information isn't imported.
4. Once the information is correct, the import is completed successfully, and the new vehicle profiles appear in the **Fleet management** application.
