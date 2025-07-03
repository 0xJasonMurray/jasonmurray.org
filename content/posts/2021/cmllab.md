---
title: "Create a network topology in Cisco Modeling Lab (CML)"
date: 2021-02-25T06:21:25-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cml
  - cisco
  - lab
  - networking
description: Guide for creating a basic networking topology in Cisco Modeling Lab (CML).
---

## Overview

[Cisco Modeling Lab](https://www.cisco.com/c/en/us/products/cloud-systems-management/modeling-labs/index.html) is a commercial network virtualization environment. All routers, switches, and security devices are licensed and included within the CML system.  Refer to [this post](/posts/2021/cml/) for more information and installation instructions.

In this guide, we will journey through the steps required to create and link a basic network topology with no configuration.

## Details

Start with a blank canvas by clicking the `Dashboard` button in the upper right corner of the screen:

[![Image of CML add lab screen](/images/2021-02-24-19-21-59.png)](/images/2021-02-24-19-21-59.png)

Click the `Add Nodes` bar of the right side of the screen to expose the available routers and servers:

[![Image of CML adding lab devices](/images/2021-02-24-19-23-13.png)](/images/2021-02-24-19-23-13.png)

From the device menu, click and drag routers onto the canvas:

[![Image of adding routers to canvas ](/images/2021-02-24-19-24-36.png)](/images/2021-02-24-19-24-36.png)

To create a network link between devices, click the router until the following menu appears. Then click and hold the blue button with the chain-links.  Drag the link to the device appropriate router:

[![Image of click the blue link button](/images/2021-02-24-19-25-13.png)](/images/2021-02-24-19-25-13.png)

When the mouse button is released, an interface menu appears.  Select the appropriate interfaces to make the connection:

[![Image of adding link between routers](/images/2021-02-24-19-25-47.png)](/images/2021-02-24-19-25-47.png)

Example lab with all four routers connected:

[![Image of all routers linked](/images/2021-02-24-19-26-39.png)](/images/2021-02-24-19-26-39.png)

If desired, CML will create basic configurations on each device.  This includes setting the hostname, enabling interfaces, and setting usernames and passwords:

[![Image of build initial lab config](/images/2021-02-24-19-31-35.png)](/images/2021-02-24-19-31-35.png)

Once the topology creation is complete, boot up devices by navigating to the `Simulate` menu and clicking the `Start Lab` button:

[![Image of start the lab](/images/2021-02-24-19-58-04.png)](/images/2021-02-24-19-58-04.png)

In the bottom right corner of each router is a status indicator.  It will turn solid green once the router is fully booted:

[![Image of lab starting up](/images/2021-02-24-19-58-58.png)](/images/2021-02-24-19-58-58.png)

We can see from this view, the lab is fully booted as indicated by the green status button by each router.  In the `Node` menu, Status, Uptime, and CPU statistics are displayed for each device:

[![Image of routers started](/images/2021-02-24-20-02-26.png)](/images/2021-02-24-20-02-26.png)

Access the console by clicking the router and selecting the grey box that looks like a console display:

[![Image of ](/images/2021-02-25-13-25-14.png)](/images/2021-02-25-13-25-14.png)

A console menu option pops up below.  Click `Open Console` in the black box:

[![Image of opening console](/images/2021-02-24-20-04-39.png)](/images/2021-02-24-20-04-39.png)

Full access is provided to the router.  By default the username and password is `cisco` / `cisco`:

[![Image of serial console](/images/2021-02-24-20-05-37.png)](/images/2021-02-24-20-05-37.png)