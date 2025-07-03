---
title: "Easy to configure network latency graphs with SmokePing"
date: 2020-07-27T07:14:14-05:00
toc: false
images:
categories:
  - tech
tags: 
  - smokeping
---

When all you need is a simple network latency graphing tool, take a look at [SmokePing](https://oss.oetiker.ch/smokeping/).  There are many open-source and free tools, but they tend to be overly complex and difficult to configure for small projects or simple graphing needs.

SmokePing is light-weight and comes preloaded with probes to graph http, dns, ssh, ping, tcp ports, and [more](https://oss.oetiker.ch/smokeping/probe/index.en.html). 

I run SmokePing for both work and personal projects.   At work we use it to graph latency between different sites and data centers.  At 60 second intervals and 365 days of historical records, it is an excellent tool for trending or troubleshooting user issues.  At home I use it to graph Internet response times to popular websites and their DNS servers.   This information is used to validate my ISP works as expected and helps to correlate outages if there are problems.   

![smokeping](/images/smokeping.png)

[Install SmokePing](https://oss.oetiker.ch/smokeping/doc/smokeping_install.en.html) per their instructions or distribution package manager.

Configure devices to monitor by editing the `Targets` file in the /etc/smokeping/config.d directory:

```
jemurray@home-server:/etc/smokeping/config.d$ cat Targets
*** Targets ***
probe = FPing
menu = Top
title = Murray Home Network Statistics
remark = To view the network statistics, choose from one of the latency menu options in the column on the left.

+ network
menu = Net latency
title = Network latency (ICMP pings)

++ Google
host = google.com
++ Spectrum
host = rns01.charter.com
++ OpenDNS
*** Targets ***
probe = FPing
menu = Top
title = Murray Home Network Statistics
remark = To view the network statistics, choose from one of the latency menu options in the column on the left.

+ network
menu = Net latency
title = Network latency (ICMP pings)

++ Google
host = google.com
++ Spectrum
host = rns01.charter.com
host = resolver1.opendns.com
++ WashU
host = anycast.ip.wustl.edu.
++ Amazon
host = amazon.com
++ Master-Bedroom-Google
host = 192.168.86.27
++ home-router
host = testwifi.here
++ Netflix
host = netflix.com

+ services
menu = Service latency
title = Service latency (DNS, HTTP)

++ DNS
probe = DNS
menu = DNS latency
title = Service latency (DNS)

+++ Google
host = dns.google

+++ Spectrum
host = rns01.charter.com

+++ OpenDNS
host = resolver1.opendns.com

+++ WashU
lookup = wustl.edu
host = anycast.ip.wustl.edu.

+++ CloudFlare
host = one.one.one.one

+++ home-router
host = testwifi.here

++ HTTP
menu = HTTP latency
title = Service latency (HTTP)

+++ Google
host = google.com

+++ OpenDNS
host = opendns.com

+++ WashU
host = www.wustl.edu

+++ Amazon
host = amazon.com

+++ home-router
host = testwifi.here

+++ Netflix
host = netflix.com
```

Restart for the changes to take affect:

```
jemurray@home-server:/etc/smokeping/config.d$ sudo /etc/init.d/smokeping restart
[sudo] password for jemurray:
[ ok ] Restarting smokeping (via systemctl): smokeping.service.
```