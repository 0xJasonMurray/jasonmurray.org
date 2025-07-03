---
title: "Safari is automatically redirecting to an HTTPS site that does not exist"
date: 2021-01-31T06:37:49-06:00
toc: false
images:
categories:
  - tech
tags: 
  - safari
  - hsts
---

# Overview

When I entered the URL`http://localhost:1313` into the Safari web browser, it automatically redirected the request to `https://localhost:1313`.  No amount of manually retyping `http` or cache clearing resolved the problem.  After a bit of research, I determined the problem is caused by the HTTP Strict Transport Security cache.  HSTS forces web browsers to only connect to a domain over the secure `https` protocol.

In this example `http://localhost:1313` is my Hugo development server.   It never used HSTS `https`, I am unsure why it Safari thought it was.   This post details the steps necessary to clear the HSTS cache to allow unencrypted `http` connections again.  

# Details

When the following URL is entered into Safari:

![Image of Safari URL bar with http://localhost:1313 entered](/images/2021-01-25-16-38-43.png)

Safari automatically redirects to the non-existent HTTPS site:

![Image of Safari web browser with https://loalhost:1313 in the URL bar because of automatic redirection](/images/2021-01-25-16-39-13.png)

Since the Hugo webserver does not speak `https` the page would fail with the following error:

![Image of Safari web browser displaying the error, can not establish a secure connection to the server](/images/2021-01-25-16-38-17.png)

The problem is caused by an improper HSTS cache entry.  This issue is resolved by opening the Safari settings by pressing `command + ,` and selecting `Privacy -> Manage Website Data...`:

![Image of Safari settings with the Manage Website Data button](/images/2021-01-25-16-44-11.png)


In the settings menu, search for the site improperly redirecting to `https`, in my example this is `localhost` and `127.0.0.1`. Delete all matching entries:

![Image of Safari site cache settings](/images/2021-01-25-16-40-07.png)


The page will now load without redirecting to the `https` page:

![](/images/2021-01-25-16-42-26.png)