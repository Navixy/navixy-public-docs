---
description: Access the MySQL database inside the Dockered Navixy platform using docker exec. Retrieve credentials from the .env file and connect to the navixy database.
---

# Dockered database access

In certain instances, direct access to the database might be necessary. This can prove beneficial when retrieving tracking data or modifying configurations (e.g. to edit a license key or to add an external application to the UI).

{% hint style="warning" %}
**Remember!** Engaging in careless tampering with the database poses a considerable risk of data corruption or loss. It is imperative that you access the database directly only if you possess unwavering confidence in your actions. If in doubt, contact [Navixy technical support](mailto:support@navixy.com) to find out how best to accomplish your task.
{% endhint %}

As the database is stored in a containerized form, direct access to the MySQL server is not available since it is not installed on the server itself. Instead, the MySQL server operates within the container. To access the database, you will need to access the container and use the correct credentials to call MySQL.

To access the database, `navixy` user is used. The password for it is specified during the initial installation of the platform and is stored in `navixy-package/.env` file. This user has full rights to modify the database.

Run the following command to access the database:

```
docker exec -it navixy-standalone-db-1 mysql -unavixy -p
```
