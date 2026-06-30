---
title: Connection setup
description: Establish a PostgreSQL connection to IoT Query, configure credentials, understand the data schema architecture, and apply connection security settings
---

# Connection setup

This guide explains how to connect to your **IoT Query** instance, which is built on **Neon PostgreSQL**.

## Connection parameters

When your **IoT Query** instance is set up, you'll receive these connection parameters via email:

<table><thead><tr><th width="164.9091796875">Parameter</th><th>Description</th></tr></thead><tbody><tr><td><strong>Host</strong></td><td>The database server address</td></tr><tr><td><strong>Port</strong></td><td>The connection port (typically 5432)</td></tr><tr><td><strong>Database name</strong></td><td>Your assigned database name</td></tr><tr><td><strong>Username</strong></td><td>Your database username</td></tr><tr><td><strong>Password</strong></td><td>Your secure database password</td></tr><tr><td><strong>SSL mode</strong></td><td>The SSL connection mode</td></tr></tbody></table>

## Data architecture

Your **IoT Query** organizes data in specific schemas:

* [`raw_business_data`](../schema-overview/bronze-layer.md#raw_business_data-structure) - Contains business data tables (users, objects, tasks, etc.)
* [`raw_telematics_data`](../schema-overview/bronze-layer.md#raw_telematics_data-structure) - Contains telematics data from client devices (GPS tracks, sensor readings)

{% hint style="info" %}
When querying data, you must specify both the schema (e.g. `raw_business_data`) and table (e.g. `objects`) name: `SELECT * FROM raw_business_data.objects LIMIT 10;`
{% endhint %}

## PostgreSQL compatibility

**IoT Query** is fully compatible with the PostgreSQL protocol. You can connect using:

* Standard PostgreSQL clients (pgAdmin, DBeaver, etc.)
* Programming languages with PostgreSQL drivers (Python, Java, Node.js)
* Business intelligence tools that support PostgreSQL (Tableau, Power BI)

## Connection string format

For programmatic access, use the standard PostgreSQL connection string format:

```sql
postgresql://username:password@host:port/database?sslmode=ssl_mode
```

Example with placeholders:

{% code overflow="wrap" %}
```sql
postgresql://client_user:your_password@db.example.cloud:5432/client_123_dwh?sslmode=require
```
{% endcode %}

## Connection security

Important security considerations:

* **SSL encryption** - Your data transmission is encrypted according to the specified SSL mode
* **Network configuration** - Your network must allow outbound connections to the provided host and port
* **Credential safety** - Your connection credentials are unique to your instance and should be kept secure

### Best practices for connections

When working with your **IoT Query**:

* **Manage idle connections** - Close connections when not in use to free up resources
* **Use connection pooling** - For applications with frequent database access
* **Store credentials securely** - Never hardcode credentials in scripts or applications
* **Set appropriate timeouts** - Configure reasonable connection timeouts for your use case

## Connection troubleshooting

If you encounter connection issues:

1. Verify your connection parameters match those in your welcome email
2. Confirm your network allows the connection to the specified host and port
3. Ensure your client supports the required SSL mode

## Next steps

After establishing a connection, proceed to the [Schema overview](../schema-overview/) section to learn about the available data structures and tables.
