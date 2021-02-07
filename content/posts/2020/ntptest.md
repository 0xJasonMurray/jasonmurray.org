---
title: "Testing a NTP server"
date: 2020-07-13T09:42:55-05:00
toc: false
images:
categories:
  - tech
tags: 
  - ntp
---

Accurate timestamps on network and server hardware is critical for daily operations and troubleshooting.  The NTP service is used to keep the clocks in sync, but how can we tell if the services is running as expected?

By running `ntpdate` with the `-q` option (query only, don't set date) we can validate the server is returning a proper answer:

```
jemurray@home-server:~$ ntpdate -q pool.ntp.org
server 163.237.218.19, stratum 1, offset 0.003985, delay 0.04179
server 72.30.35.88, stratum 2, offset 0.002441, delay 0.05338
server 74.6.168.72, stratum 2, offset 0.000726, delay 0.09357
server 98.191.213.1, stratum 2, offset -0.004938, delay 0.07932
13 Jul 09:42:28 ntpdate[9050]: adjust time server 163.237.218.19 offset 0.003985 sec
```
