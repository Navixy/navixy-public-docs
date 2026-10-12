---
description: Reset the Navixy admin panel password on-premise using a Java command-line tool. Use when the password is lost or changed without authorization.
---

# Password reset

While using Navixy's On-Premise platform, you might need to reset your Admin panel password. This can happen in several situations, including:

* You forgot or lost your password.
* Someone with access to the admin panel has changed the password without notifying you, particularly if multiple people have access to Admin Panel.

In either scenario, it's important to reset the admin panel password to ensure the security of your On-Premise instance. Below you will find a password reset guide that can help you recover your password.

## Steps to change the password for Admin Panel

Here are the steps you can follow to reset your Admin Panel password on Navixy's On-Premise platform:

1. Navigate to the directory where the 'api-server' is located. Enter the following prompt:
2. Linux: `cd /home/java/api-server`
3. Windows, enter: `cd C:\java\api-server`
4. Execute the following command:

```
java -cp api-server.jar com.navixy.tool.PanelPasswordSet
```

3. Confirm the password change by typing `'yes'`, and you will be prompted to enter a new password.
4. Enter the desired password, and you will be asked to confirm the new password setting operation. Type `'yes'` to continue and confirm the password setting.

{% include "https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/~/reusable/cXM93TrDUyPWrNkS4xqQ/" %}

3. You will see a notification that the password has been successfully changed. You can now log in with your new password.

{% hint style="info" %}
It's important to note that resetting your admin panel password should only be done when necessary and with caution. Keep your new password safe and secure to prevent unauthorized access to your On-Premise instance. If you experience any issues during the password reset process, contact Navixy's technical support team for assistance.
{% endhint %}
