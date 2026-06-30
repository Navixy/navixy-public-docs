---
description: Navixy App Connect is an authentication middleware that lets third-party applications integrate with Navixy using your existing credentials.
---

# Navixy App Connect

## What is Navixy App Connect?

**Navixy App Connect** is an authentication middleware that enables third-party applications to integrate with the Navixy platform using your existing credentials. It acts as a secure bridge between Navixy's authentication system and external applications, allowing you to access custom tools without creating separate accounts or managing additional passwords.

When you access an integrated application, Navixy App Connect validates your session and provides the application with secure access to your data. All authentication and authorization is handled automatically through your Navixy account.

The middleware operates through a standardized, API accessible [integration contract](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/user/applications/app-connect) that defines how external applications authenticate with Navixy.

## How it works

Navixy App Connect operates through a standardized integration contract that external developers implement in their applications. When you access an integrated application:

1. The application requests authentication through Navixy App Connect
2. Your Navixy session key is converted into a secure JWT token
3. The application receives authorized access based on your Navixy permissions
4. You work with the application using your existing Navixy credentials

This process is transparent to you as a user. You access the application through your Navixy account without additional authentication steps.

Watch this video to get a quick and visual walkthrough of authenticating a 3rd-party app with Navixy session using App Connect:

{% embed url="https://youtu.be/y8Wwob-Uw3I?si=6sbQXkTvQW2bgmb8" %}

## What value does it provide?

### Unified access control

All integrated applications respect your Navixy user roles and permissions. When your access rights change in Navixy, those changes automatically apply to all connected applications. This ensures consistent security policies across your entire platform ecosystem.

### Simplified credential management

You maintain a single set of credentials for Navixy and all integrated applications. This eliminates the need to remember multiple passwords, reduces security risks from credential reuse, and simplifies account management.

### Secure data access

Applications access your data through Navixy's authentication layer rather than requiring direct database credentials. Your sensitive information remains protected while applications receive the access they need to function effectively.

### Integrated user experience

Moving between Navixy features and third-party applications requires no additional login steps. This creates a cohesive experience where integrated tools feel like natural extensions of the Navixy platform.

## Who is App Connect for?

### Application developers

Developers building custom applications that work with Navixy data can integrate their tools without implementing complete authentication infrastructure. Whether you're creating internal business tools or commercial applications, Navixy App Connect handles user authentication and authorization automatically.

### System administrators

Platform administrators can control which applications users can access through the [User applications](./) feature. This provides centralized management of your application ecosystem while maintaining security standards.

### End users

Platform users gain access to approved third-party applications without managing separate accounts. Applications become accessible with a single sign-on experience, maintaining security while improving productivity.

### Example: Dashboard Studio

[Dashboard Studio](https://marketplace.navixy.com/shop/dashboard-studio/) is a Navixy application that demonstrates how Navixy App Connect works in practice. When you access Dashboard Studio:

* Your Navixy session authenticates you automatically
* Dashboard Studio receives secure access to your IoT data based on your permissions
* You create and manage dashboards without entering separate credentials
* Your session remains active across both Navixy and Dashboard Studio

Dashboard Studio serves as an example of what's possible with Navixy App Connect. Any third-party developer can build similar integrations for specialized use cases, industry-specific tools, or custom business applications.

## Developing with Navixy App Connect

If you're a developer interested in integrating your application with Navixy App Connect, the following resources provide complete implementation guidance:

* [API Documentation](https://app.gitbook.com/s/6dtcPLayxXVB2qaaiuIL/user-api/backend-api/resources/commons/user/applications/app-connect#required-api-endpoint): Technical specifications and implementation guidelines, endpoints and parameters

Applications you develop can be used privately within your organization or published to the [Navixy Marketplace](https://marketplace.navixy.com/) for broader distribution. Marketplace applications undergo review to ensure they meet platform standards and security requirements.
