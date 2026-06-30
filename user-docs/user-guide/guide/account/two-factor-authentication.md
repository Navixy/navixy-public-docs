---
description: Enable two-factor authentication for your Navixy account. A one-time passcode sent to your email is required alongside your password on each login.
---

# Two-factor authentication

Navixy offers two-factor authentication (2FA) to bolster security across web and mobile applications. With increasing amounts of sensitive telematics data being managed, 2FA provides a vital layer of security by requiring both a password and a one-time passcode (OTP) sent via email. Your GPS/telematics service provider determines whether 2FA is enabled and configures the related settings.

**How it works**

The OTP is sent to your email and must be entered to complete the login process. The extra step ensures that even if your password is compromised, unauthorized users can't access your account without having access to your email.

{% stepper %}
{% step %}
## Log in with your password

Enter your username and password to start the login process.
{% endstep %}

{% step %}
## Receive a one-time code

After verifying your password, you receive a one-time passcode at the email associated with your Navixy account
{% endstep %}

{% step %}
## Enter the code

Enter this OTP to complete your login.
{% endstep %}

{% step %}
## Access your account

If the OTP is correct, you gain access to your account.
{% endstep %}
{% endstepper %}

![Login verification window](../../.gitbook/assets/2fa.png)

Each passcode is valid for 5 minutes, limiting the opportunity for misuse. Additionally, you can request a new code if needed, but only once per minute to prevent abuse.
