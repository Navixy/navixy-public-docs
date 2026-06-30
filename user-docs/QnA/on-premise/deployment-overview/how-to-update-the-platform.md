---
title: How to update the platform?
description: Run automated update scripts on Linux or perform manual updates on Windows. Back up database before starting to enable rollback if needed
---

# How to update the platform?

## Where can I download the update package of Navixy On-Premise platform?

The update package is not different from the installation package. What you need is the latest build of the platform that is available at:

[https://get.navixy.com/latest](https://get.navixy.com/latest)

## What precautions should be taken before starting the update?

Although the update is a fairly simple and common process, it is a direct database change, so it is a good practice to back up the database, data disk, or the entire instance - depending on your capabilities. This will allow you to roll back if the update doesn't go as planned.

## How difficult is the update process?

For Linux instances of any type, the update is automated. All you need to do is run the script, specify the necessary data (such as the database password), and then wait for the process to complete.

For Windows, the update is done manually. The automated update wizard is under construction.

Update instructions are always available on Navixy website.

## Can I automate the update delivery?

No. The update must be performed manually and controlled carefully. Although it is a common and even routine task, any minor failure in the process can cause the platform to become inoperable. To avoid negative consequences, your technician team should manually perform the update process, paying attention to everything that happens on the screen.

## How often are new versions of the platform released?

Navixy doesn't have a strict release cycle. The average release period for major versions is 3 months. Major versions are released after significant innovations on the platform and the introduction of new features. Minor versions usually contain bug fixes and minor innovations, and can be released up to several times in a month.

## How do I know that the new version is released?

Check the release notes on Navixy website or ask the technical solutions team.

## Should I update my instance every time the new version is released?

No. This is not mandatory, but it is a good practice. You can install updates as often as you want and at times that you find convenient.

## How do I know that my platform is outdated?

Your platform may be considered outdated when:

* You have not received updates for more than six months
* You miss the new features mentioned in our release notes
* You do not have a device supported on our platform available for registration.
