---
title: "Intercepting and Editing HTTP Requests with Burp Suite"
date: 2020-12-21T16:39:39-06:00
toc: false
images:
categories:
  - tech
tags: 
  - burp
---

# Overview

[Burp Suite Community Edition](https://portswigger.net/burp/communitydownload) is a tool for exploring `http` transactions between a client and the server.
We can intercept, edit, decode, and examine all requests by proxying browser traffic through burp suite.  

# Examples

To keep it simple, explore Burp with the built in browser:

![](/images/2020-12-21-16-56-31.png)



Viewing a webpage with Intercept turned off.  In this mode the website flows freely through burp:

![](/images/2020-12-21-16-59-45.png)


When `intercept is on`, each transaction is stopped for inspection:

![](/images/2020-12-21-16-57-40.png)

Here we can edit the request before `forwarding` it onto the server.  For example, I will edit the `GET /` and change it to `GET /posts` before clicking `Forward`:

![](/images/2020-12-21-16-50-35.png)

Results from editing the `GET`:

![](/images/2020-12-21-16-51-48.png)

All transactions are saved in the `HTTP History` tab:

![](/images/2020-12-21-16-53-46.png)