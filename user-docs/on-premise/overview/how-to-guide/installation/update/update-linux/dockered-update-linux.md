---
description: Update Dockered Navixy on-premise on Linux. Prepare the environment, stop containers, unpack the update package, and restore configuration files.
---

# Dockered update - Linux

Similar to any software, the Navixy platform necessitates regular updates to incorporate all the latest innovations and enhancements. While the PaaS version automatically handles this, On-premise instances require customers to request and install updates themselves.

For the Dockered version, the update process is simplified to the maximum extent, making it accessible even to those with basic system administration skills.

{% hint style="danger" %}
While the skill requirements may be low, it remains imperative to exercise utmost caution during the update process and diligently adhere to the instructions. This ensures that sensitive data is safeguarded from inadvertent damage.
{% endhint %}

## Preparing for update

The basic configuration of the Dockered platform is in the `.env` file located in the `/navixy-package` directory. Open this file in a text editor and check the value specified for `WORKDIR`, which is the directory containing the platform's critical files: database and all service configs. It can be located either in `/navixy-package` directory itself (default value - `./work` ), or in any other location on your server.

Once you have checked where the files are located, proceed with the update.

## Update process

**Step 1.** Make sure you are in the `/navixy-package` directory and execute this command:

```
docker compose down
```

This will stop the currently running platform.

**Step 2** - optional but recommended. Make a backup of `.env` file and `WORKDIR` directory.

**Step 3.** Unpack the new Navixy platform distribution package (where `<PACKAGE_NAME>` is the name of the file):

```
tar -zxvf <PACKAGE_NAME>.tar.gz
```

{% hint style="info" %}
If you perform unpacking in the same location where the existing `/navixy-package` resides, **this will overwrite the current platform files**. However, the package does not contain `.env` file and `./work` directory, so your data is not endangered.
{% endhint %}

**Step 4.** Place the `.env` file from the old directory to the newly unpacked `/navixy-package`. If it is already there, check its contents and make sure that domain(s) and other values are specified correctly.

**Step 5.** If the `WORKDIR` value is `./work`, move the `/work` directory from old to new `/navixy-package`. If the work directory has a custom path, or if it is already in a new directory, skip this step.

**Step 6.** Make sure you are in the newly unpacked `/navixy-package` and run the following command:

```
docker compose up -d
```

This will launch a new version of the platform. All you have to do is wait.

## Finishing the update

After successfully completing the above steps, you can delete the old `/navixy-package` directory if it has not been overwritten with the new one.

To verify the availability of your platform, access the Admin Panel and user interface. If all functions are operating properly, the update has been successfully implemented.
