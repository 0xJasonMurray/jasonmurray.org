---
title: "Enable equal cost multi-path with iBGP"
date: 2021-02-28T11:18:19-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - networking
  - ibgp
  - bgp
  - cml
description: Two active network paths to the same destination is known as `equal cost multi-path` (ECMP). This post examines the routing table before and after enabling ECMP and the BGP configuration required to enable ECMP.

---

## Overview

Two active network paths to the same destination is known as `equal cost multi-path` (ECMP). With two paths in the routing table, a link failure will instantly shift all traffic to the active link. In addition, traffic is load balanced over each active link, effectively doubling the bandwidth to the destination.

Internal routing protocols such as [is-is](/posts/2021/basicisis/), ospf, and eigrp automatically load balance multiple paths:


[![Image of ](/images/2021-02-28-16-01-01.png)](/images/2021-02-28-16-01-01.png)

BGP does not load balance multiple paths without additional configuration, only one route is installed in the routing table (FIB). Additional routes are waiting in the `bgp` database (RIB), ready to be installed if the active path fails:

[![Image of ](/images/2021-02-28-16-01-35.png)](/images/2021-02-28-16-01-35.png)

This post will examine the routing table before and after enabling ECMP and the BGP configuration required to enable ECMP.


## Lab Topology Diagrams

This lab uses [Cisco Modeling Lab](/posts/2021/cmllab/) and builds upon the [is-is](/posts/2021/basicisis/) and [bgp](/posts/2021/basicbgp/) configurations in previous posts.

Cisco Modeling Lab topology:

[![Image of basic bgp topology](/images/2021-02-25-10-23-41.png)](/images/2021-02-25-10-23-41.png)

Logical topology with organization, name and addressing information:

[![Image of ](/images/2021-02-25-12-55-11.png)](/images/2021-02-25-12-55-11.png)

## Details

In this lab, BGP is carrying the routes within the `192.168.192.0/19` network range.


From the `csr1000v-0` router, we see a single route installed in the routing table to the alpine-linux at `192.168.200.2`:

```
csr1000v-0#show ip route 192.168.200.2
Routing entry for 192.168.192.0/19, supernet
  Known via "bgp 65000", distance 200, metric 0
  Tag 65100, type internal
  Last update from 192.168.0.5 00:16:01 ago
  Routing Descriptor Blocks:
  * 192.168.0.5, from 192.168.0.1, 00:16:01 ago
      Route metric is 0, traffic share count is 1
      AS Hops 1
      Route tag 65100
      MPLS label: none
```

In the BGP database (RIB), there are two paths (192.168.0.5 and 192.168.0.7) to the alpine-linux host at `192.168.200.2`. In the event of the primary link failure, the secondary path will be installed in the main routing table (FIB):

```
csr1000v-0#show ip bgp 192.168.200.0
BGP routing table entry for 192.168.192.0/19, version 10
Paths: (2 available, best #1, table default)
  Not advertised to any peer
  Refresh Epoch 1
  65100
    192.168.0.5 (metric 20) from 192.168.0.1 (192.168.100.1)
      Origin IGP, metric 0, localpref 100, valid, internal, best
      rx pathid: 0, tx pathid: 0x0
      Updated on Feb 25 2021 17:03:10 UTC
  Refresh Epoch 1
  65100
    192.168.0.7 (metric 20) from 192.168.0.3 (192.168.100.2)
      Origin IGP, metric 0, localpref 100, valid, internal
      rx pathid: 0, tx pathid: 0
      Updated on Feb 25 2021 17:03:10 UTC
```

The following `bgp` configuration, without ECMP, is installed on the `csr1000v-0` router: 

```text
router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.1 remote-as 65000
 neighbor 192.168.0.3 remote-as 65000
 !
 address-family ipv4
  neighbor 192.168.0.1 activate
  neighbor 192.168.0.3 activate
 exit-address-family
```


To enable ECMP, add the line `maximum-paths ibgp 2` to the iBGP configuration:

```text
router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.1 remote-as 65000
 neighbor 192.168.0.3 remote-as 65000
 !
 address-family ipv4
  neighbor 192.168.0.1 activate
  neighbor 192.168.0.3 activate
  maximum-paths ibgp 2
 exit-address-family
```


After enabling ECMP, the routing table contains two routes to the alpine-linux `192.68.200.2` host:

```
csr1000v-0#show ip route 192.168.200.2
Routing entry for 192.168.192.0/19, supernet
  Known via "bgp 65000", distance 200, metric 0
  Tag 65100, type internal
  Last update from 192.168.0.7 00:00:23 ago
  Routing Descriptor Blocks:
    192.168.0.7, from 192.168.0.3, 00:00:23 ago
      Route metric is 0, traffic share count is 1
      AS Hops 1
      Route tag 65100
      MPLS label: none
  * 192.168.0.5, from 192.168.0.1, 00:00:23 ago
      Route metric is 0, traffic share count is 1
      AS Hops 1
      Route tag 65100
      MPLS label: none
```

Validate multiple paths with `traceroute`. I added comments (!) to the traceroute output detailing each of the paths:

```
csr1000v-0#traceroute 192.168.200.2
Type escape sequence to abort.
Tracing the route to 192.168.200.2
VRF info: (vrf in name/id, vrf out name/id)
  1 192.168.0.1 3 msec   ! PATH 1
    192.168.0.3 2 msec   ! PATH 2
    192.168.0.1 1 msec   ! Back to PATH 1
  2 192.168.0.7 2 msec
    192.168.0.5 2 msec
    192.168.0.7 2 msec
  3 192.168.200.2 [AS 65100] 3 msec 2 msec 3 msec
```

