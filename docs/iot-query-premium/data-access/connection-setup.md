---
description: >-
  The connection details of IoT Query Premium, the clients that can connect,
  and a first test query that confirms your access to the data.
tags:
  - reference
  - preview
---

# Connection setup

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

IoT Query Premium is a ClickHouse database that you connect to with your own client or application. This page lists the connection details, the clients that work with ClickHouse, and a test query that confirms that you can read your data. It is step 1 of the developer path through [Private Telematics Lakehouse](./), for developers and data engineers. Before you start, get IoT Query Premium and your connection details as described in [Getting started](../getting-started.md).

## Connection details

The administrator of your organization receives these details when IoT Query Premium is added to the plan of your organization. Each workspace has its own set, and a connection reads the data of its workspace only. Each set has the following details:

| Detail | Example value | What it is |
| --- | --- | --- |
| Host | `<HOST>` | The address of your IoT Query Premium database |
| Port | `<PORT>` | The port of the ClickHouse HTTP interface, which BI tools and most drivers use |
| Native port | `<NATIVE_PORT>` | The port of the ClickHouse native protocol, which `clickhouse-client` and some drivers use |
| User name | `<USER>` | The user that your queries run as |
| Password | `<PASSWORD>` | The password of that user |

Store the password in a secret store or an environment variable, not in code or in a dashboard definition. Anyone who has the password can read the data of the workspace.

## Databases that one connection reads

One connection reads four ClickHouse databases. In ClickHouse, a database is a named group of tables, like a schema in PostgreSQL. Write each table name with its database, for example `core_bdr.asset`.

| Database | Layer | What it contains |
| --- | --- | --- |
| `core_bdr` | Raw data | Business data from Business Data Repository: workspaces, devices, assets, geo objects, schedules, and custom fields |
| `telemetry` | Raw data | Positions and sensor values that your GPS devices send |
| `report` | Transformation | Trips and stops, calculated by Navixy |
| `agg` | Transformation | Sensor values and digital inputs per minute, and positions in geo objects, calculated by Navixy |

See [Data schema](data-schema/) to find the tables of each database.

## Clients that can connect

Any client that supports ClickHouse can connect to IoT Query Premium. The following clients are common choices:

* **SQL clients** for exploring the data, such as `clickhouse-client` and DBeaver.
* **BI tools** with a ClickHouse connector: Power BI, Apache Superset, Grafana, and Streamlit. See [Use third-party BI tools](third-party-bi-tools/).
* **Drivers** for your own applications: the official ClickHouse drivers for Python (`clickhouse-connect`), Java (JDBC), Go, and Node.js, and the ClickHouse ODBC driver.

Queries to IoT Query Premium use ClickHouse SQL, which is close to standard SQL. [Data schema](data-schema/#rules-for-every-query) describes the differences that matter for IoT Query Premium.

## Connection security

Connections to IoT Query Premium are encrypted with TLS. Enable TLS or SSL in your client, for example with `--secure` in `clickhouse-client` or `https://` in an HTTP URL.

## Test the connection

Run a short query before you set up a BI tool. It confirms that the host, port, and credentials work, and that your user can read the data.

To count the assets of your workspace over the HTTP interface, send the following request:

```bash
curl -L \
  --request POST \
  --url 'https://<HOST>:<PORT>/' \
  --user '<USER>:<PASSWORD>' \
  --data-binary 'SELECT count() AS assets FROM core_bdr.asset FINAL WHERE deleted_at IS NULL FORMAT JSON'
```

The response has the column list, the rows, and the query statistics:

```json
{
  "meta": [
    {
      "name": "assets",
      "type": "UInt64"
    }
  ],
  "data": [
    {
      "assets": "42"
    }
  ],
  "rows": 1,
  "statistics": {
    "elapsed": 0.004,
    "rows_read": 42,
    "bytes_read": 2016
  }
}
```

ClickHouse returns 64-bit integers as strings in JSON, so `"42"` is a string. `FINAL` and `deleted_at IS NULL` make the query count each current asset once. [Data schema](data-schema/#read-the-current-version-of-each-row) explains why.

To run the same check in `clickhouse-client`, connect with the following command and enter the password when the client asks for it:

```bash
clickhouse-client --host <HOST> --port <NATIVE_PORT> --secure --user <USER> --password
```

Then run the query:

```sql
SELECT count() AS assets
FROM core_bdr.asset FINAL
WHERE deleted_at IS NULL
```

The client prints one row with the number of assets:

```
┌─assets─┐
│     42 │
└────────┘
```

## Connection errors

The following errors are the most common on a first connection:

| Error | Cause | What to do |
| --- | --- | --- |
| `Authentication failed` | The user name or password is wrong | Copy both again from your connection details. Check that no space was added before or after the password |
| Connection timed out or refused | The host or port is wrong, or a firewall blocks the port | Check the host and port. Ask your network administrator to allow outgoing connections to that port |
| Connection reset, or an unexpected packet | The client connects without TLS | Enable TLS or SSL in the client settings |
| Certificate error | The client doesn't trust the certificate of the server | Update the trusted certificates of your operating system or client. Don't turn off certificate checks |
| `UNKNOWN_TABLE` | The table name has no database, or the name is misspelled | Write the table with its database, for example `core_bdr.asset` |

## Next steps

* [Data schema](data-schema/): Learn which tables each layer contains and how they join.
* [Use third-party BI tools](third-party-bi-tools/): Choose a BI tool and connect it to IoT Query Premium.
