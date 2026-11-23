---
description: >-
  How to set the server address for GPS devices and the addresses used in
  links after a Windows installation, and check the database after each update.
tags:
  - legacy
---

# Checks after installation and update - Windows

After you install or update the Navixy platform on Windows with the scripts, check the settings that the scripts leave to you:

* Set the server address for GPS devices after the installation. The installation script sets a local address there.
* Check the addresses and HTTPS in links if your platform was first installed with build 2026.10.356 or earlier. Later installation scripts set them.
* Check the database version after each update. The update scripts of builds 2026.10.354 to 2026.10.356 can report a completed update when the database part failed.

This page is for administrators of Navixy On-Premise on Windows Server. On a single-server installation, do all steps on that server. Only administrators can open the files in `C:\java`, so edit them in Notepad run as administrator. The MySQL commands ask for the `navixy` MySQL user's password that you entered during installation.

## Set the server address for GPS devices

The Navixy platform sends its server address to GPS devices in configuration commands during automatic device activation. The installation script sets this address to `127.0.0.1` and `localhost`, which GPS devices can't reach. Usually, GPS devices connect to your service domain.

1. On the application server, run Notepad as administrator.
2. Open `C:\java\tcp-server\conf\config.properties`.
3. In the `externalIP=` line, enter the public IP address that GPS devices connect to.
4. In the `externalHostname=` line, enter the domain that GPS devices connect to.
5. Save the file.
6. In **Services**, restart **Navixy tcp-server**.

The update script keeps `config.properties`, so you set these values only once. Some licenses are bound to the `externalIP` value. If **Navixy tcp-server** stops after the change, restore the previous value and contact Navixy technical support.

## Set the API address for links to files

Links to uploaded files and images, such as user avatars and report logos, use the API address from the api-server settings. Installations made with the Windows scripts up to build 2026.10.356 have `http://localhost/api` there, so users can't open these links. The correct address is your service domain with `/api`, for example `https://my.company.com/api`. If your platform doesn't use HTTPS, the address starts with `http://`. The update script of build 2026.10.357 and later sets this address when it finds your service domain in the Nginx settings.

Installations made up to build 2026.10.356 also have no file storage, so file uploads in forms fail. The update script of build 2026.10.357 and later sets up file storage during the database update. If it warns that file storage isn't set up, contact Navixy technical support.

1. On the application server, run Notepad as administrator.
2. Open `C:\java\api-server\conf\config.properties`.
3. If the `api.externalBaseUrl=` line has `http://localhost/api`, replace it with the correct address.
4. Save the file.
5. In **Services**, restart **Navixy api-server**.

## Check the domain in links

Links in emails and reports use the domain from the service preferences in the Navixy Admin Panel. Examples are password recovery links and account activation links. A new database has the sample domain `my.domain.net` there. Installations made with the Windows scripts up to build 2026.10.356 keep this sample domain, and the update script doesn't change it. From build 2026.10.357, the installation script sets your service domain there. If you set the domain during the [final steps of the installation](automatic-installation-windows.md#accessing-user-interface), it's already correct.

1. Log in to the Navixy Admin Panel.
2. Go to **Settings** → **Service preferences** → **Domain and URLs**.
3. If the domain isn't your service domain, enter your service domain.
4. Save your changes.

For more information, see [Domain name](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/settings/domain-name).

## Turn on HTTPS in links

Links in emails and reports start with `https://` only when your platform's `has_https` setting is `1`. Installations made with the Windows scripts up to build 2026.10.356 never set it. From build 2026.10.357, the installation script sets it when you choose SSL. The update script of build 2026.10.357 and later sets it on a single-server installation that uses HTTPS. On a two-server installation, the update script doesn't set it, because the database server has no web server settings to take HTTPS from. Do these steps only if your platform opens over HTTPS.

1. On the database server, open Command Prompt as administrator.
2. To see the setting, run the following command:

   ```
   mysql -u navixy -p google -e "SELECT has_https FROM paas_settings WHERE dealer_id=1"
   ```

   The command prints `1` when HTTPS is turned on and `0` when it isn't.
3. If the command prints `0`, turn on HTTPS with the following command:

   ```
   mysql -u navixy -p google -e "UPDATE paas_settings SET has_https=1 WHERE dealer_id=1"
   ```

   The command prints nothing. To check the change, repeat step 2.
4. On the application server, restart **Navixy api-server** in **Services**.

The restart is needed because api-server keeps this setting in memory.

## Check the database version after each update

The update scripts of builds 2026.10.354 to 2026.10.356 can report a completed update when the database migrations failed. In that case, their output shows a failed migration step but no error text. The database stays on the previous version, and the new version of the application runs on the old database structure. From build 2026.10.357, the update script stops with an error in this case and doesn't update the application. On a two-server installation, check the database version before you update the application server.

1. In the update package, open `navixy-package\db\VERSION` in a text editor.
2. Note the package version in the file, for example `navixy-standalone-provider.2026.10.356`.
3. On the database server, open Command Prompt as administrator.
4. Run the following command to see the database version:

   ```
   mysql -u navixy -p google -e "SELECT value FROM variables WHERE var='dist_build_version'"
   ```
5. Compare the two versions.

If the versions are the same, the database part of the update finished.

### Update the database again

If the database has the previous version, run the database part of the update again. It's safe to repeat. The update script can't log in to MySQL with a password that contains a double quote (`"`). The update scripts of builds 2026.10.354 to 2026.10.356 also can't apply database migrations when the password contains a space.

1. On the database server, run the update script.
2. Enter `2` to update only the database.
3. Enter the MySQL host, port, user, and password. Use a user whose password has no space and no double quote.

   If the file `C:\ProgramData\Navixy\mysql-root.cnf` exists, the script can use the MySQL `root` password from it. To use this password, press Enter at each prompt.
4. When the script finishes, check the database version again.
5. If the database still has the previous version, contact Navixy technical support.
