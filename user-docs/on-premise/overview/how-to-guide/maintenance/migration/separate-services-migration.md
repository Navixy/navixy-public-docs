---
description: Migrate individual Navixy services to a custom directory or separate server. Update configuration files to reconnect the database, web server, and other dependencies.
---

# Separate services migration

As your platform is being used and the number of registered devices grows, you may need to redistribute or move certain services to other directories or servers. This is typically the case for database and web services. Backend services (Java services) are strongly recommended to be in the original installation location.

{% hint style="info" %}
Migrating the database and website is currently only applicable to a "classic" installation, where the platform is installed as a system service. Dockered platform currently exists as all-in-one and does not imply distribution to multiple servers.
{% endhint %}

## Database migration

Depending on your objectives, you may need to move the database to a custom directory within the server or migrate it to a separate server.

### Custom database directory

By default, the database files are located inside a designated subdirectory within the MySQL directory: `/var/lib/mysql`. Suppose you need to move the database to a custom directory named `/DB`. To do this, perform the following steps:&#x20;

1. Create sub-directories for files and logs, for example:

```
/DB/mysql-files 
/DB/mysql-log
```

&#x20;2\. Change owner to _mysql_ and permissions to `drwxr-x---`

```
chown mysql:mysql mysql-files 
chown mysql:mysql mysql-log 
chmod 750 mysql-files 
chmod 750 mysql-log
```

&#x20;3\. Stop mysql: `systemctl stop mysql`.

&#x20;4\. Copy **(not move)** files from `var/lib/mysql` and `var/log/mysql` to corresponding new directories

&#x20;5\. Edit the configuration in `/etc/mysql/mysql.conf.d/mysqld.cnf`. Change `datadir` and `log error` strings to new values.

&#x20;6\. Start mysql: `systemctl start mysql`. Check its `error.log` for errors.

### Moving database to separate server

If you need to move the database to a separate server (for example, for load balancing), you need to perform the following steps.

1. Install MySQL of the same version on the new server.
2. Make a backup of your database.
3. Restore the backup on the new server and start the database.
4. Change the configuration of Java services: `api-server`, `sms-server`, `tcp-server`. In the directory of each service there is a `db.properties` file specifying the database connection parameters. For example, on Linux such files are located at the following paths:

* `/home/java/api-server/conf/db.properties`
* `/home/java/sms-server/conf/db.properties`
* `/home/java/tcp-server/conf/db.properties`\
  Edit each file and change `localhost` to the IP address of your new database server. Preserve all the other contents.

5. Save the files and restart the Java-services.
6. Check the services operation and their logs. If they fail to run, check the connection to the new server on port 3306 (default port for MySQL).

## Web server migration

Typically, the website (frontend) is hosted on the same server as the Java services (backend), and this is the case with most of our clients' servers, even the largest ones. However, you may want to host the web server and all the website files on a dedicated server for more flexible load management and network access.

To accomplish this, you will need to do the following:

1. Install Nginx on the dedicated server.
2. Move the frontend directories to the new server:

* `/etc/nginx`
* `/var/www`

3. Check the `navixy.conf` and `navixy_ssl.conf` (if exists) and specify the `{backend_server}` address - where the Java services are hosted - in the following lines:

```
location / { 
    proxy_pass http://{backend_server}:8084; 
    } 
location /console  { 
    proxy_pass http://{backend_server}:8383/console; 
    } 
location /event {  
    proxy_pass http://{backend_server}:8084/event;
    }
location /file/upload/ { 
    proxy_pass   http://{backend_server}:8084/file/upload/; 
    } 
location /file/dl/ { 
    proxy_pass   http://{backend_server}:8084/file/dl/; 
    }
```

4. Start Nginx.
