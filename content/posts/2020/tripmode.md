---
title: "Limiting bandwidth on macOS with TripMode"
date: 2020-12-09T15:04:33-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tripmode
  - network
  - bandwidth
---

It is not uncommon for my macOS laptop to kick off a large software update, backup event, or an iCloud synchronization at the most inopportune time, such as roaming while on the iPhone hot spot or during a virtual meeting when a broadband link is bandwidth limited.  To help ease this congestion, I use [TripMode](https://tripmode.ch/).  

TripMode shapes bandwidth by blocking specified applications from accessing the network.

To simplify configuration, the profile switching feature automatically blocks applications depending on the connected network.  

When connected to the home wireless network TripMode is disabled:

![](/images/2020-12-09-15-59-24.png)

TripMode automatically detects and enables application blocking when connected to my iPhone hot spot `JEM iPhone`:

![](/images/2020-12-09-16-01-32.png)

Another view that shows how I block backup software when TripMode is enabled:

![](/images/2020-12-09-15-39-19.png)

But allow Teams, messages, and the terminal application to access the network:

![](/images/2020-12-09-15-33-16.png)



