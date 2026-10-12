---
title: Manage admin credentials (On-premise)
description: Rename admin account in the google.dealers table or reset password via API server. Log in to Admin Panel and use the password change feature
---

# Manage admin credentials (On-premise)

## Can I rename the admin account so that I can access Admin Panel using a login of my choice?

Yes, but this isn't recommended.

You can do this in `google.dealers` table, and you need to update the `login` value. The default value is `admin` and you can change it to whatever you need. Be sure to remember the updated login value.

## How do I change my Admin Panel password?

If you know your current password, log in to your account and then use the password change feature in the upper right corner.

If you have lost your current password, you need to reset it. This reset is done on the application server by directly contacting the API server. Follow the instruction below.

## Links

* [Password reset](https://www.navixy.com/docs/on-premise/on-premise/how-to-guide/maintenance/server-credentials/password-reset)
