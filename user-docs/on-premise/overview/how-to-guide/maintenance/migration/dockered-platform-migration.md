---
description: Migrate the Dockered Navixy platform to a new server by copying the navixy-package directory, updating network access configuration, and restarting containers.
---

# Dockered platform migration

Navixy dockered platform functions as an all-in-one solution, so the migration process is not divided into steps, but is done all at once.

To run the platform on the new server, you first need to install the Docker software to ensure the containers can launch.

```sh
curl -fsSL https://get.docker.com -o install-docker.sh
sh ./install-docker.sh
```

After installing Docker, you can proceed with the migration.

## Container migration

First you need to go to the working folder of your platform, where all its system files are located. This is `navixy-package` directory. It does not have a default location and can be located anywhere selected during the initial installation.

Find a file named `.env` in this directory and check its contents. If the value of `WORKDIR` is different from the default `./work`, remember this location.

Now you need to stop the containers. While in `navixy-package` directory, execute the following command:

```
docker compose down
```

After this the platform will be stopped.

Take the entire `navixy-package` directory and copy it entirely to the new server. If the value of `WORKDIR` is different `./work`, then copy that directory as well, preserving the full path.

## Launching the platform

Once you have copied all the platform files, you need to make the new server available to clients and devices. Since the new server has a different IP address, you need to do one of two things:

* Switch the public IP address from the old server to the new server.
* Switch the DNS record from the old public IP to the new one.

Now you can proceed to `navixy-package` directory on your new server and launch the platform. To do this, while in the directory, run the following command:

```
docker compose up -d
```

The platform will need some time to run all the services, and after this it will be fully operational and ready for use.

{% hint style="danger" %}
If you experience any problems during or after the migration, you can contact Navixy tech support at [support@navixy.com](mailto:support@navixy.com) for further consultation.
{% endhint %}
