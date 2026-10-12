---
description: >-
  Run your own copy of Dashboard Studio from its open-source code and connect
  it to IoT Query Premium.
tags:
  - guide
  - preview
---

# Open-source Dashboard Studio

{% hint style="info" %}
IoT Query Premium is coming soon. This page describes how it works at release. To learn when it becomes available for your organization, [contact Navixy](https://navixy.com/en/contact?utm_source=navixy-docs\&utm_campaign=iot-query-premium).
{% endhint %}

Dashboard Studio is open source, under the Mozilla Public License 2.0. You can run your own copy, change its interface, and build it into your own application. For example, you can show dashboards under your brand or inside your own portal. This guide takes you from the source code to a local copy that reads IoT Query Premium data. It also points to the deployment guide for a server. It is for development teams that run and change their own copy.

Before you start, you need:

| What you need | Details |
| --- | --- |
| Node.js and npm | Node.js 20 or later, npm 8 or later |
| Docker | Needed by the one-command setup and by the container deployment |
| Git | To get the source code |
| IoT Query Premium connection details | The host, port, user name, and password of your workspace. See [Connection setup](../data-access/connection-setup.md) |
| A settings database | A PostgreSQL database with the `dashboard_studio_meta_data` schema, where your copy stores menus, dashboards, reports, and preferences |
| Skills | React, TypeScript, SQL, and web development |

## 1. Get the source code

The source code is in the public repository [Navixy/navixy-iot-query-dashboard](https://github.com/Navixy/navixy-iot-query-dashboard) on GitHub. It has the React front end, the Node.js backend, and the documentation of both.

To get the code, run the following command:

```bash
git clone https://github.com/Navixy/navixy-iot-query-dashboard.git
cd navixy-iot-query-dashboard
```

The repository is a read-only copy of the code that Navixy develops. Send questions about the repository to your Navixy support channel. Don't open issues on GitHub.

## 2. Configure the backend

The backend reads its settings from `backend/.env`. It doesn't start without `JWT_SECRET`, the key that the backend uses to sign the sessions of your users.

To configure the backend, follow these steps:

1. Copy the example file:

   ```bash
   cp backend/.env.example backend/.env
   ```

2. Generate a secret key:

   ```bash
   openssl rand -hex 32
   ```

3. In `backend/.env`, paste the key as the value of `JWT_SECRET`.
4. If people can open your copy from the internet, set `ALLOWED_DB_HOST_SUFFIXES`. Its value is the domains of the databases that your copy may connect to. Without this setting, a user can sign in with any database that the backend can connect to.

The repository README lists every variable of `backend/.env`.

## 3. Start Dashboard Studio

To install the dependencies and start the backend and the front end, run the following command in the root of the repository:

```bash
npm run dev:setup
```

The command checks Node.js, npm, and Docker, starts Redis in a container, installs the dependencies, and starts both servers. When it finishes, open `http://localhost:8080` in your browser. The backend runs on port 3001.

To stop Dashboard Studio, run the following command:

```bash
npm run dev:stop
```

{% hint style="warning" %}
`npm run dev:setup` and `npm run dev:stop` stop every Vite process on the computer, including the development servers of other projects. It also removes every stopped Docker container on the computer, not only the containers of Dashboard Studio.
{% endhint %}

## 4. Sign in and connect to IoT Query Premium

Your copy has no passwords of its own. At sign-in, each user enters the connection details of the two databases that the copy works with:

* **IoT database connection**: The database that every query reads. Enter the details of IoT Query Premium here. Dashboard Studio never writes to it.
* **User settings database connection**: The database where your copy stores the menu, the dashboards, the reports, and the preferences of each user.

To sign in, follow these steps:

1. Open `http://localhost:8080`.
2. In **Email**, enter your email address. Your copy creates a user for it at the first sign-in.
3. In **Role**, select **Admin**, **Editor**, or **Viewer**. In your own copy, the role that you choose here decides what you can do in Dashboard Studio. Anyone who can open the sign-in page can choose **Admin**, so limit who can open your copy.
4. Under **IoT database connection**, select **Connection parameters**.
5. Enter your IoT Query Premium details in **Host**, **Port**, **Database**, **User**, and **Password**, and turn on **Use SSL**.
6. Click **Test IoT connection**.
   The form confirms that the connection works.
7. Under **User settings database connection**, enter the details of your settings database.
8. Click **Test user settings connection**.
9. Click **Sign in**.
   Dashboard Studio opens on the **Home** page.

{% hint style="warning" %}
If the sign-in fails with a message that the `dashboard_studio_meta_data` schema or its tables don't exist, the settings database isn't prepared. Your copy never creates the schema. Ask whoever set up the database to create it.
{% endhint %}

To check that your copy reads IoT Query Premium data, click **Tools** on the sidebar, select **SQL editor**, and run a first query, such as the query in [Connection setup](../data-access/connection-setup.md#test-the-connection). Then continue with [Create dashboard](create-dashboard.md).

## 5. Deploy your copy to a server

For a copy that other people use, run Dashboard Studio in containers on a Linux server. The deployment guide of the repository, `docs/DEPLOYMENT.md`, describes the steps. You need:

* A Linux host with Docker Engine and the Docker Compose v2 plugin.
* A domain name, a TLS certificate, and Nginx on the host.
* A `backend/.env.docker` file with the same settings as `backend/.env`, including `JWT_SECRET`.

To embed Dashboard Studio in your own web application, set `FRAME_ANCESTORS` to the addresses of the pages that show it. Without this setting, any website can embed your copy.

## 6. Change the code

Before you change the code, read the documentation in the `docs` folder of the repository:

| Document | What it describes |
| --- | --- |
| [ARCHITECTURE.md](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/docs/ARCHITECTURE.md) | The design of Dashboard Studio, its components, and how data moves between them |
| [DEVELOPMENT.md](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/docs/DEVELOPMENT.md) | Local setup, coding standards, tests, and workflows |
| [API.md](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/docs/API.md) | The endpoints of the backend, their requests and responses, and authentication |
| [DEPLOYMENT.md](https://github.com/Navixy/navixy-iot-query-dashboard/blob/main/docs/DEPLOYMENT.md) | Production hosting on a Linux server |

Common changes are your own colors and fonts, new panel types, a different sign-in, and dashboards inside your own portal.

Under the Mozilla Public License 2.0, a file of Dashboard Studio that you change stays under the same license. You make the source of that file available to the people who receive your copy. Your own files in the same product can use any license.

When Navixy updates the repository, merge the updates into your copy. Then test your own changes again before you deploy.

## Next steps

* [Create dashboard](create-dashboard.md): Build a first dashboard in your copy.
* [Write SQL queries](write-sql-queries.md): Write panel queries over IoT Query Premium data.
* [Data schema](../data-access/data-schema/): Learn which tables your copy can read.
