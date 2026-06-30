---
description: Manage credentials for Navixy on-premise servers. Covers password policies, admin panel access, SSH key authentication, and support team access requirements.
---

# Server credentials

Like any information system, Navixy platform must be password-protected to avoid unauthorized access and data loss. In the case of On-Premise servers, you must also take care of protecting the server infrastructure, which also implies setting passwords. Below we gathered the most common questions regarding passwords that arise when working with the On-premise version of Navixy platform.

### Will Navixy's support team require remote access to my On-premise instance?

Navixy's support team does not have nor require remote access to your On-premise instance. However, in some cases, you may choose to grant them permission to connect to your server for troubleshooting or maintenance purposes. This remote connection can be established through SSH or other means.

It's important to note that any remote access will only be conducted with your explicit permission. After the support team has completed their work, it is recommended that you change your login credentials as an additional security measure.

It's worth emphasizing that Navixy takes security seriously and will not access your server without your authorization. Additionally, any access granted to the support team will be used solely for the purpose of resolving technical issues and maintaining the system's functionality.

### What kind of credentials might Navixy's tech support require?

Depending on the issue being addressed, Navixy's tech support team may require different types of credentials. In most cases, troubleshooting can be done with access to the Admin panel and user credentials. However, in exceptional cases, such as when diagnosing backend services or making changes to the database, more extensive access may be necessary. This might include:

* Login credentials or private keys to access the server (depending on the type of authorization used)
* Passwords for the database
* VPN credentials (if needed to connect to the server)

It's important to note that Navixy's staff will never ask for passwords related to your email or other services not related to the Navixy platform. Additionally, any credentials that are shared with Navixy's support team will be kept confidential and not shared with any third parties. As a best practice, it is recommended to change any credentials that were shared with the support team after the work has been completed to maintain security.

### Can Navixy's team provide me with the password to regain access my On-premise server or Admin Panel?

Navixy's technical support team can only access the password for the admin panel if you have previously provided it to them and have not changed it since then. However, it's important to note that even if the technical support staff has access to your server and database, they will not know the password because it is stored in an encrypted form.

It's worth emphasizing that Navixy takes the security of your account information seriously and has implemented various measures to ensure that sensitive information, including passwords, are kept secure. Additionally, any credentials that are shared with Navixy's support team will be kept confidential and not shared with any third parties. As a best practice, it's recommended to change any passwords that were shared with the support team after the work has been completed to maintain security.

### What should I do if I lost my Admin Panel password for my On-premise instance?

If you have lost your admin panel password for your On-premise instance, there is no password reset feature available through the interface. However, there are steps you can take to recover your password. First, refer to the [Password reset](password-reset.md) guide provided by Navixy. If that doesn't help, you can contact Navixy's technical support team for assistance. Note that resetting the password will require direct access to your server, so it's important to grant permission for this access.

It's important to note that losing your admin panel password can be a serious security issue, so it's important to take measures to prevent this from happening in the future. Consider implementing strong password policies, and consider using password management tools to securely store your passwords. Additionally, you may want to limit [access](https://app.gitbook.com/s/KdgeXg71LpaDrwexQYwp/access) to the admin panel to only authorized personnel to reduce the risk of unauthorized access.

### What are the password requirements for Admin Panel?

The following requirements are present:

{% include "https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/~/reusable/cXM93TrDUyPWrNkS4xqQ/" %}
