---
title: Connecting Apache Superset
description: Connect Apache Superset to IoT Query via the PostgreSQL driver. Configure the database connection and build interactive fleet monitoring dashboards
---

# Connecting Apache Superset

In this article, we will walk through the process of connecting Apache Superset to the analytical database. To make it more illustrative, we will also build a dashboard that connects to the analytical database and monitors the real-time status of vehicles, all using Apache Superset.

{% hint style="info" %}
This guide is part of the **IoT Query** documentation suite and specifically covers connecting Power BI to your data warehouse. If you're still deciding which BI tool to use, refer to the [Selecting BI tools](./) overview.
{% endhint %}

## Dashboard features

* Display total number of objects
* Visualize vehicle movement statuses (moving/stopped/parked)
* Visualize connection statuses (active/idle/offline)
* Detailed table with current status of all vehicles
* Filtering by vehicle type, group, movement status, and connection status
* Data and report export capabilities
* Customizable notifications and alerts

## Technical requirements

* Docker and Docker Compose
* Minimum 4 GB RAM (8 GB recommended)
* 20 GB of free disk space
* Linux/Windows with WSL2/macOS
* Python 3.8+
* Internet access for database connection

## Installation and setup

### 1. Installation with Docker (recommended method)

1. Install Docker and Docker Compose by following the official documentation:

* [Docker Installation](https://docs.docker.com/engine/install/)
* [Docker Compose Installation](https://docs.docker.com/compose/install/)

2. Download the official docker-compose file:

{% code overflow="wrap" %}
```sh
curl -fL https://raw.githubusercontent.com/apache/superset/master/docker-compose-non-dev.yml -o docker-compose.yml
```
{% endcode %}

3. Start Superset:

```sh
docker-compose up -d
```

4. Create an administrator:

```sh
docker-compose exec superset superset fab create-admin \
  --username admin \
  --firstname Superset \
  --lastname Admin \
  --email admin@superset.com \
  --password admin
```

5. Initialize the database:

```sh
docker-compose exec superset superset db upgrade
```

6. Load examples and initialize roles:

```sh
docker-compose exec superset superset init
```

### 2. Installation with pip (for development)

1. Create a virtual environment:

```sh
python -m venv venv
source venv/bin/activate  # for Linux/macOS
# or
.\venv\Scripts\activate  # for Windows
```

2. Install Superset:

```sh
pip install apache-superset
```

3. Initialize the database:

```sh
superset db upgrade
```

4. Create an administrator:

```sh
export FLASK_APP=superset
superset fab create-admin
```

5. Load examples and initialize roles:

```sh
superset init
```

6. Start Superset:

```sh
superset run -p 8088 --with-threads --reload --debugger
```

## Database connection

1. Log in to Superset (default: [http://localhost:8088](http://localhost:8088))
2. Navigate to Data → Databases
3. Click "+" to add a new database
4. Fill in the connection parameters:
   1. Database: PostgreSQL
   2. SQLAlchemy URI: `postgresql://${DB_USER}:${DB_PASS}@${DB_HOST}:${DB_PORT}/${DB_NAME}`
   3. Display Name: Analytics Database
   4. Extra: `{"engine_params": {"connect_args": {"sslmode": "require"}}}`
5. Click **Test Connection** to verify the connection
6. Save the settings

### Connection parameter reference

<table><thead><tr><th width="177.9090576171875">Lakehouse Parameter</th><th>Apache Superset Setting Location</th><th>Notes</th></tr></thead><tbody><tr><td><strong>Host</strong></td><td><code>DB_HOST</code> in SQLAlchemy URI</td><td>The database server address provided in your welcome email</td></tr><tr><td><strong>Port</strong></td><td><code>DB_PORT</code> in SQLAlchemy URI</td><td>Default is 5432 for PostgreSQL</td></tr><tr><td><strong>Database name</strong></td><td><code>DB_NAME</code> in SQLAlchemy URI</td><td>Your assigned database name</td></tr><tr><td><strong>Username</strong></td><td><code>DB_USER</code> in SQLAlchemy URI</td><td>Your database username</td></tr><tr><td><strong>Password</strong></td><td><code>DB_PASS</code> in SQLAlchemy URI</td><td>Your secure database password</td></tr><tr><td><strong>SSL mode</strong></td><td><code>connect_args</code> in Extra parameters</td><td>Set to <strong>require</strong> in the Extra JSON configuration</td></tr><tr><td><strong>Schema</strong></td><td>Dataset configuration</td><td>Specify schema (<code>raw_business_data</code> or <code>raw_telematics_data</code>) in each dataset</td></tr></tbody></table>

## Dashboard and chart import

1. Clone the [bi-integratons](https://github.com/SquareGPS/bi-intergrations) repository:

```sh
git clone https://github.com/SquareGPS/bi-intergrations.git
```

2. In Superset, go to **Settings → Import/Export**
3. Import the files in the following order:
   1. `datasets.json` - datasets
   2. `charts.json` - charts
   3. `dashboards.json` - dashboards
4. After importing, update the database connections in each dataset

## Troubleshooting

### Database connection issues

* **Connection error:** Check the correctness of credentials and connection parameters
* **Firewall error:** Ensure your IP address is added to the allowlist
* **SSL issues:** Check SSL settings in connection parameters

### Performance issues

* **Slow visualization loading:**
  * Optimize SQL queries
  * Reduce the number of simultaneously displayed elements
  * Use result caching
* **High memory usage:**
  * Increase Docker container resources
  * Optimize database queries

### Other issues

Here are some tricks that can help you fix common issues:

1. Check Superset logs:

```
docker-compose logs superset
```

2. Restart containers:

```
docker-compose restart
```

3. Clear browser cache
4. Check Superset version and update if necessary

## Next steps

After successfully connecting Power BI to your **IoT Query** instance, we recommend you to:

* Explore the available data schemas by reviewing the [Schema overview](../../schema-overview/) section to better understand the data structure and relationships.
* Start with simple queries focused on specific business entities before building complex dashboards - check our [example queries](../../../example-queries/) for reference.

### **Support**

For technical questions or requests for access to the demonstration database, please contact: [support@squaregps.com](mailto:support@squaregps.com)
