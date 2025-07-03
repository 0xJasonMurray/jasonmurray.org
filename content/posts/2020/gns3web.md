---
title: "GNS3 web interface"
date: 2020-09-13T05:45:05-05:00
toc: false
images:
categories:
  - tech
tags: 
  - gns3
---

# Overview

For those people using the GNS3 VM, you may not need to download the [GNS3 desktop client](https://www.gns3.com/software/download) anymore. The [GNS3 VM](https://www.gns3.com/software/download-vm) comes with a web interface enabled by default.


# Accessing the web interface

Main topology menu:

![main interface](/images/gns3webmenu.png)

Viewing a project:

![main interface](/images/gns3webtopo.png)

Accessing the router through the integrated web console:

![main interface](/images/gns3webconsole.png)


# Remote access over an SSH tunnel

My GNS3 server is physically located in my home lab.   As someone who likes to test new ideas while on the road, I can start a SSH tunnel to access the lab from any remote location.  In fact, I am writing this post and working in the lab from a hotel room.

GNS3 over an SSH tunnel:

```
ssh -L 3080:192.168.86.40:3080 murray-home.ddns.net
```

Open a web browser and connect to:

```
http://localhost:3080/
```

I prefer to use a terminal session as the console.  The existing SSH tunnel can dual purpose as a way to access the console:

```
jemurray@home-server:~$ telnet 192.168.86.40 5039
Trying 192.168.86.40...
Connected to 192.168.86.40.
Escape character is '^]'.


root>

root>

root> show ?
Possible completions:
  access-cac           Show access cac under enhanced-broadband-edge
  access-security      Show access security information
  accounting           Show accounting profiles and records
  agent                Show SDN agent information
  amt                  Show AMT Protocol information
  ancp                 Show ancp information
  aps                  Show Automatic Protection Switching information
  arp                  Show system Address Resolution Protocol table entries
  as-path              Show table of known autonomous system paths
  authentication-whitelist  Show 802.1X White List MAC addresses
  ....
  ```