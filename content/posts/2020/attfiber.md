---
title: "AT&T residential fiber service - 1G FTTH"
date: 2020-09-23T17:16:25-05:00
toc: false
images:
categories:
  - tech
tags: 
  - attfiber
---

# Overview

AT&T 1G fiber service was installed at the house today.  The fiber service was pulled into our neighborhood about 2 years ago.  I put off switching because of the cost at first, then it was the hassle of switching services.  Now that the entire family is either working or doing online schooling from home, it was time to upgrade.   The 100 down / 7 up Spectrum service is no longer adequate.  

... and symmetric speeds are glorious.

# Symmetric Speed

Checking the speeds with iPerf3 to one of the PerfSonar servers at WashU.  Not quite 1Gb/s, but I think the limit is the Google router.  I will look into that later.  Over 600Mb/s both up and down is good enough for now:

```
jemurray@home-server:~$ iperf3 -c 128.252.5.113 -R
Connecting to host 128.252.5.113, port 5201
Reverse mode, remote host 128.252.5.113 is sending
[  4] local 192.168.86.5 port 43380 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bandwidth
[  4]   0.00-1.00   sec  46.9 MBytes   393 Mbits/sec
[  4]   1.00-2.00   sec  75.4 MBytes   632 Mbits/sec
[  4]   2.00-3.00   sec  77.7 MBytes   652 Mbits/sec
[  4]   3.00-4.00   sec  82.6 MBytes   693 Mbits/sec
[  4]   4.00-5.00   sec  90.1 MBytes   756 Mbits/sec
[  4]   5.00-6.00   sec  71.3 MBytes   598 Mbits/sec
[  4]   6.00-7.00   sec  55.9 MBytes   469 Mbits/sec
[  4]   7.00-8.00   sec  61.5 MBytes   516 Mbits/sec
[  4]   8.00-9.00   sec  71.1 MBytes   597 Mbits/sec
[  4]   9.00-10.00  sec  88.9 MBytes   746 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   735 MBytes   617 Mbits/sec  710             sender
[  4]   0.00-10.00  sec   723 MBytes   607 Mbits/sec                  receiver

iperf Done.


jemurray@home-server:~$ iperf3 -c 128.252.5.113
Connecting to host 128.252.5.113, port 5201
[  4] local 192.168.86.5 port 43532 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  36.3 MBytes   305 Mbits/sec    0   1.78 MBytes
[  4]   1.00-2.00   sec  94.4 MBytes   792 Mbits/sec    0   3.31 MBytes
[  4]   2.00-3.00   sec   106 MBytes   893 Mbits/sec    2   3.31 MBytes
[  4]   3.00-4.00   sec  97.9 MBytes   821 Mbits/sec    0   3.31 MBytes
[  4]   4.00-5.00   sec  83.8 MBytes   703 Mbits/sec  534   1.68 MBytes
[  4]   5.00-6.00   sec  83.3 MBytes   699 Mbits/sec    0   1.78 MBytes
[  4]   6.00-7.00   sec  87.9 MBytes   738 Mbits/sec    0   1.85 MBytes
[  4]   7.00-8.00   sec  89.5 MBytes   751 Mbits/sec    0   1.90 MBytes
[  4]   8.00-9.00   sec  90.8 MBytes   761 Mbits/sec    0   1.94 MBytes
[  4]   9.00-10.00  sec  91.0 MBytes   764 Mbits/sec    0   1.96 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   862 MBytes   723 Mbits/sec  536             sender
[  4]   0.00-10.00  sec   860 MBytes   721 Mbits/sec                  receiver
```

# Pass-through to the Google WiFi router

Plugging my Google WiFI router directly into the ONT does not work, you must use the AT&T supplied router.  To avoid double NATing, the AT&T router needs to be configured as a passthrough.  

First plug the Google router into one of the LAN ports on the AT&T router.

Then, log into the AT&T router through a web browser to the address: `http://192.168.1.254/`.  Select `Firewall -> IP Passthrough` and select the Google router:

![ATT PassThrough](/images/attpassthrough.png)

I could use the ATT router, but I like the mesh network Google WiFi creates.  It covers the house better.

# Picture Tour

Fiber entry point to the house:
![ATT Fiber](/images/attfiberentry.jpg)

Fiber service loop:
![ATT Fiber](/images/attfiberslack.jpg)

Fiber splice point:
![ATT Fiber](/images/attfibersplice.jpg)

Fiber that will be buried soon:
![ATT Fiber](/images/attfiber1.jpg)

Fiber that will be buried soon:
![ATT Fiber](/images/attfiber2.jpg)

Optical Network Terminal:
![ATT Fiber](/images/attontfront.jpg)


Optical Network Terminal side view:
![ATT Fiber](/images/attont.jpg)

ATT supplied router:
![ATT Fiber](/images/attrouter.jpg)

Ports on the back of the router:
![ATT Fiber](/images/attrouterback.jpg)
