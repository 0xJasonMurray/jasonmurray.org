---
title: "Google Chrome: thisisunsafe SSL error bypass"
date: 2021-01-10T17:01:38-06:00
toc: false
images:
categories:
  - tech
tags: 
  - google
  - chrome
  - thisisunsafe
---

# Overview

If Google Chrome presents an SSL error with no option to accept the risk, type `thisisunsafe` to bypass the error and continue loading the page.  

**WARNING:** Make sure the risk is fully understood.  Someone may be attempting to impersonate the site or steal your personal information. 

# Details

When Google Chrome detects an SSL condition or error they believe is unsafe for users to view, the following error is presented:

![](/images/2021-01-10-17-01-53.png)

Other browsers, such as Firefox present a button which allows users to 'Accept the Risk' and continue, Chrome does not.

There is a hidden option users can enter to 'Accept the Risk' and continue to the site without having to load the page in another browser.  When presented with this error message, the user needs to type: `thisisunsafe` all one word with no space.

Once entered Chrome will load the page.