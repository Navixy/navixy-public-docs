---
description: Enable extended device logging in Navixy on-premise by editing the TCP-server configuration. Captures detailed operation data for diagnosing device-specific issues.
---

# Extended logging

Sometimes devices do not work with the platform as expected. Problems can be of different nature, and in most cases are related to a particular device, or a specific model, or non-standard firmware.

In such cases, advanced logs can be exceptionally useful. There you can track all the details of the device's operation on the platform.

To enable extended logs, do the following:

1. Edit the `config.properties` file of **tcp-server**:
  - Linux: `/home/java/tcp-server/conf/config.properties`
  - Windows: `C:\java\tcp-server\conf\config.properties`
2. Add the following line to the bottom of the config (replace `<IMEI>` with the actual device IMEI):

```
debug.traceDevices=<IMEI>
```

For multiple IMEIs:

```
debug.traceDevices=<IMEI1>,<IMEI2>
```

example:

```
debug.traceDevices=33225566,44772211
```

4. Restart **tcp-server** service. This will apply the config and create a `/debug` subdirectory in `.../tcp-server/log/`.
5. Wait for the issues or trigger them
6. Check the `/log/debug` directory. It will contain a file(s) named `trace-<IMEI>.log`.
7. Stop the extended logging when you no longer require it, otherwise new logs will keep consuming disk space. To do this, comment out (with `#` symbol) or delete the previously added line, and then restart **tcp-server**.