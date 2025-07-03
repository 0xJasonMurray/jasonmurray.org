---
title: "Removing cookie pop-ups with uBlock Origin"
date: 2020-06-15T06:06:45-04:00
toc: false
images:
categories:
  - tech
tags: 
  - ublockorigin
  - cookie
  - gdpr
---

# Summary

Websites that require users to accept the "We use cookies..." pop-up messages is an unfortunate side effect of the [GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) regulation.  While I fully support a users control over their data and the added benefits regulations such as GDPR provide, the pop-up messages are too excessive.

The [uBlock Origin](https://github.com/gorhill/uBlock) `EasyList Cookie` filter will automatically remove these annoying pop-up messages.

# Details

Example website with the "We use cookies..." pop-up message:

![site with cookie](/images/sitewithcookie.png)

Enable the [EasyList Cookie filter](https://easylist.to/) within the [uBlock Origin](https://github.com/gorhill/uBlock) settings:

![uBlock origin cookie settings](/images/ublockcookie.png)

Enjoy the web `cookie message` free:

![no more cookie](/images/nocookie.png)

