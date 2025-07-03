---
title: "Redirecting URLs with Cloudflare Page Rules"
date: 2021-02-16T09:32:08-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cloudflare
  - redirect
  - url
description: Configuring Cloudflare Page Rules as an alternative to link shorteners.  
---

## Overview

For technical users, the Cloudflare [Page Rules](https://support.cloudflare.com/hc/en-us/articles/200172286-Configuring-URL-forwarding-or-redirects-with-Cloudflare-Page-Rules) service offers an alternative to link shorteners such as [bit.ly](https://bitly.com/) and [Owly](http://ow.ly/).  All domains configured within your Cloudflare account are available to use with the Page Rule URL redirection service.

Once configured, users can redirect a URL such as: `https://example.com/my-redirect` to `https://example-of-a-convoluted-url-that-is-difficult-to-remember.org/some-long-extension`.

## Details

Navigate to the `Cloudflare dashboard -> Page Rules menu` then select `Create Page Rule`:

[![Image of cloudflare page rules menu](/images/2021-02-16-09-53-13.png)](/images/2021-02-16-09-53-13.png)

Create the `Page Rule` forwarding event by filling in the following fields.  In this example, I will redirect `jasonmurray.org/calendar` to my public Office 365 calendar:

- Source URL: `jasonmurray.org/calendar`
- Type: `Forwarding URL`
- Redirect type: `301 - Permanent Redirect`
- Destination URL: `example.com/very-long-url-that-users-would-not-want-to-type-in`

[![Image of filled in cloudflare page rules](/images/2021-02-16-09-55-11.png)](/images/2021-02-16-09-55-11.png)

Wait a few minutes for the changes to deploy, then enter `https://jasonmurray.org/calendar` in the URL bar:

[![Image of browser with URL bar going to jasonmurray.org/calendar ](/images/2021-02-16-09-47-36.png)](/images/2021-02-16-09-47-36.png)

The domain redirects to the very long Office 365 public calendar URL:

[![Image of office 365 public calendar](/images/2021-02-16-09-48-56.png)](/images/2021-02-16-09-48-56.png)