---
description: Restart Dockered Navixy containers using docker ps to list running services and docker restart to reload specific backend components.
---

# Restarting dockered instance

Restarting the Navixy dockered platform may become necessary in various troubleshooting and configuration scenarios.

To begin, you should create a list of the containers on your server, including both running and stopped ones. This can be accomplished using a command like the following:

```
docker ps -a
```

Output for this command is quite big and detailed, so you can shorten it like this:

```
docker ps -a --format "table {{.Names}}\t{{.Status}}"
```

This will return a list of containers and their uptime. To restart any of them, you need to run `docker restart` command and list the required containers. For example, to restart all three backend Java services, use this command:

```
docker restart navixy-standalone-api-1 navixy-standalone-sms-1 navixy-standalone-tcp-1
```

{% hint style="info" %}
Alternatively, you can use `docker compose restart` command listing the required containers after it. In this case, the command does exactly the same thing.
{% endhint %}
