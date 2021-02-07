---
title: "iOS14 supports default opening of third party apps"
date: 2020-09-20T12:34:01-05:00
toc: false
images:
categories:
  - tech
tags: 
  - ios
  - defaultapplication
---

New to iOS14 is a feature I have been requesting for years.   The ability to select the default application.  Without this feature, if an app needed to display a web page, send an email, or map an address, the only option was to use the software provided by Apple.  There was no option to open these links using third party apps.  While many people were fine using Apples Safari, mail, or maps, I was not.  I prefer Chrome, Gmail, and Waze.

Apple listened.  Starting with iOS14, users can select their preferred default application.  However, not every app is supported.  The developers must provide hooks into this feature. For example, Gmail can not be set as the default mail client yet.

For those apps that do, a quick toggle in settings is all that is required.

From settings, select the desired default application.  In this example, Chrome:

![chrome default app settings](/images/chromeapplicationsettings.PNG)

Select `Default Browser App`, then choose `Chrome`:

![chrome default app settings](/images/chromedefault.PNG)

When an app needs to open a link, Chrome will process the request.

