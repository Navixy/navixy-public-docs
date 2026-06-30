---
description: Run the automated MySQL update script for Navixy on Debian 9/10 or CentOS 7. The script performs compatibility checks and installs the required MySQL version.
---

# MySQL update script for Debian and CentOS

The Navixy platform, like any modern software, undergoes continuous improvement and updates. As a result, new versions of the platform are unable to run on outdated third-party applications. Since many of our valued customers rely on our platform for their business needs, it is inevitable that the third-party software installed during the initial deployment becomes outdated over time.

Working with a database is an integral part of the platform, which means that outdated versions of the DBMS eventually become unsuitable for efficient operation. However, tampering with a database always carries some level of risk, leaving many customers hesitant to do it themselves. Furthermore, the standard update process requires advanced system administration skills, which may not be readily available to everyone.

At Navixy, we understand these challenges and strive to provide a seamless and user-friendly experience for our customers. We consistently work to enhance the platform's performance and ensure compatibility with the latest technologies, so you can focus on running your business with peace of mind.

## How To

To enhance convenience for our valued customers and automate the DBMS update process, we have developed a MySQL update script. This script effortlessly handles the update without the need for your active participation. However, certain conditions must be met to ensure a successful execution:

* Operating System (OS): Our script supports **Debian 9 or 10,** as well as **CentOS 7**, which are widely utilized by our customers.
* MySQL Version: Please ensure you are running **MySQL version 5.7.34** or newer.

The script performs thorough checks to verify the compatibility of the OS and MySQL versions. If any inconsistencies are detected or if MySQL is not installed, the script will provide a warning message and take no further action.

{% hint style="info" %}
It is important to note that the script must be executed with ROOT privileges. The update process will be carried out online, and the MySQL server will be restarted.
{% endhint %}

To initiate the update, kindly request the script from Navixy tech support. Once obtained, please place the mysqlUpdate.sh script in any convenient folder (e.g., /home/navixy/), or on the Database server if your Navixy instance is deployed on two servers.

Finally, execute the script with elevated privileges. You will be able to monitor the installation progress, as the script prepares for the upgrade, downloads the latest version, and installs the necessary MySQL components. No manual intervention is required; simply be patient and wait for the process to complete.

We strive to ensure a seamless experience and appreciate your trust in our solutions. Feel free to reach out to our tech support team should you require any assistance.

{% hint style="info" %}
Please note: When installing on Debian 9, there is a specific requirement - you will be prompted to press Enter once. The remaining steps of the process are automated.
{% endhint %}

The duration of the update may vary significantly, based on factors such as the database size and server performance.

## Video

Below is a video of the MySQL 5.7 upgrade process performed on Debian 10. As can be seen, the whole process is completely automatic, without any special actions on behalf of a server administrator. As a result, MySQL 8.0 is installed on the server and ready to work.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FkUnMmePH99SsdChtqqu7%2Fuploads%2FnPcgRxu0OrC3iKqyE9cd%2Fmysql-deb.mp4?alt=media&token=9356fc9c-88b3-4f8c-b45a-e35885dc480b" %}

## The outcome

Our script tackles the aforementioned issues, sparing customers from the hassle of handling the update themselves. Instead, they simply need to execute a straightforward command and patiently await the results. Subsequently, with an updated MySQL, clients gain the opportunity to upgrade the platform to the latest version and reap the benefits of all the innovations it offers.
