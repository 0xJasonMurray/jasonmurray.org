---
title: "Using public route servers and looking glass(es) to validate BGP prefix announcements"
date: 2020-05-14T10:35:33-05:00
categories:
  - tech
tags: [til, bgp, routeserver, lookingglass]
---

[Public route servers](http://www.routeservers.org/) and [BGP looking glass(es)](https://www.google.com/search?q=bgp+looking+glass&oq=bgp+looking+glass) are used to validate BGP announcements.  These tools are helpful to validate BGP changes are working as expected across the public Internet.


```
$ telnet router-server.he.net

route-server> show bgp ipv4 128.252.0.0/16
BGP routing table entry for 128.252.0.0/16
Paths: (23 available, best #11, table Default-IP-Routing-Table)
  Not advertised to any peer
  20115 2552 2552 2552
    216.66.24.70 from 216.218.252.82 (216.218.252.169)
      Origin IGP, metric 0, localpref 140, valid, internal
      Originator: 216.218.252.169, Cluster list: 216.218.252.82 216.218.252.184
      Last update: Wed Apr 29 15:57:23 2020
```

Web based looking glass from Internet2:

![Web Looking Glass](/images/lookingglass.png)
