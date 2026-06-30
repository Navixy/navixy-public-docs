---
description: Create and manage user roles in Navixy to define what each team member can view and manage, from vehicle tracking to reporting and geofence editing.
---

# Role management

A **User role** in Navixy is a set of permissions and access rights assigned to a user, defining what they can view and manage within the Navixy platform. User roles are customizable and allow the owner to control the level of access each user has to different features and data. Roles determine a user’s ability to:

* Manage vehicles, employees, and assets
* View telematics data from GPS devices and sensors
* Work with geofences and points of interest/Places (POIs)
* Generate reports, use specific app plugins and tools

By assigning roles, the owner can ensure that users have the appropriate access needed to perform their tasks without compromising the security or integrity of the business data and operations. Each role can include basic rights available to all users, as well as additional rights tailored to the specific needs and responsibilities of different users.

## Adding and managing roles

To add a new role, navigate to **Account Settings → Users** and roles and select the **Role management** tab. Click the **Add role** button (the button with the **+** sign).

![](../../../.gitbook/assets/image-20240718-043236.png)

2. **Create a new role**:

* Think of a name for the role.
* Select the necessary access rights for this role.
* Click Save to create the role.

### Types of rights

All rights are divided into three categories: **Owner**, **Basic**, and **Additional**.

#### Owner rights

Owner rights are available only to the account owner (main user) and can't be assigned to other roles. These include:

* Add and edit users
* Manage object groups
* Configure data forwarding
* Change plan

**Basic rights**

Basic rights are available to all roles and can't be disabled. These include:

* Map tools (layers, route planning, etc.)
* Trip and event history
* Service operations
* Interactive widgets (change work status, change output states, etc.)
* Access to linked data (assigned tasks, control rules, etc.)

**Additional rights**

Additional Rights are configured individually for each role. These include:

* **Device settings (software side)**: Modify device settings related to software, including name, work status, LBS radius, sensors and buttons, parking detection, and more.
* **Device settings (hardware side)**: Edit settings for specific devices, such as tracking mode, phone number, special parameters like harsh driving, ignition source, and others.
* **Vehicle systems control**: Control various vehicle states through the **Outputs** widget, including engine shutdown, car alarm, doors, and other systems.
* **Device activation**: Add and activate new tracking devices. After activation, GPS devices are enabled for both the current user’s account and the owner’s account.
* **Reports**: Create, view, and configure standard or scheduled reports. Each user can only view their individual reports.
* **Alert rules**: Create, modify, and assign rules to objects to maintain efficient operations and tracking protocols.
* **Tags**: Create and edit tags. Assign and use tags to search for various elements, including objects, geofences, drivers, vehicles, and more.
* **Tasks**: Create and modify individual and scheduled tasks. This option also grants the right to edit data in forms attached to tasks.
* **Geofences**: Create and edit circular, route, and polygonal geofences, a key mapping tool for tracking objects and ensuring security.
* **Places**: Create and edit points of interest (POI), a map function that allows managing important locations and improving delivery control operations.
* **Employees**: Manage the organizational structure, create and edit employee and driver profiles, and create departments.
* **Vehicles**: Create, modify, and distribute fleet profiles by department. This option also allows assigning drivers to specific vehicles.
* **Billing and payments**: Provide financial and accounting groups access to payment management, transaction history, and low balance alert settings.
* **Video telematics**: Access live streams, view events, and play videos from DVRs and MDVRs, enhancing road safety and fleet security.
* **Weblocator**: Share the real-time location of tracked objects with third parties through a plugin that can be embedded in a website or application.
* **Courier on the map**: Allow customers to track their orders on the website, offering real-time location updates and courier route progress.
