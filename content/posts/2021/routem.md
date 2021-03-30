---
title: "Generating random BGP prefixes with routem"
date: 2021-03-29T20:54:58-05:00
toc: false
images:
categories:
  - tech
tags: 
  - routem
  - bgp
  - networking
  - cml
description: Generating random BGP prefixes with routem
---

## Overview

In a previous post, I used [exabgp](/posts/2020/exabgp-fulltable/) and a BGP snapshot from [RIPE](https://www.ripe.net/analyse/internet-measurements/routing-information-service-ris/ris-raw-data) to simulate a full Internet routing table.

For those looking for a less complicated way to simulate large routing tables, the `routem` software will generate any number of prefixes and random AS paths with a few lines of configuration.

## Network Topology

This lab uses the following eBGP router topology and IP addresses:

- isp-1 (172.16.200.1) <-> external-linux-1 (172.16.200.2)
- isp-1 (192.160.0.1) <-> edge-2 (192.168.0.0)
- isp-1 (192.168.0.3) <-> edge-1 (192.168.0.2)

[![Image of ](/images/2021-03-29-21-00-33.png)](/images/2021-03-29-21-00-33.png)

## external-linux-1 server

Create the `bgp.txt` configuration file for `routem` which builds 3 sets of networks each with 500 routes and random length AS paths:

```
router bgp 65300
neighbor 172.16.200.1 remote-as 65200
neighbor 172.16.200.1 update-source 172.16.200.2
network 1 1.0.0.0/24 500
network 2 2.0.0.0/24 500
network 3 3.0.0.0/24 500
aspath 1 random 5
nlricount 1
redoattr 1
sendall
```

Start `routem` by running `routem -f bgp.txt`:

```
external-linux-1:~$ routem -f bgp.txt 
Version 2.1(8)(routem_june04_2008) by khphan on Wed Jun 4 11:03:24 PDT 2008
Copyright (c) 1998-1999, 2002-2003, 2007 by cisco Systems, Inc.
All rights reserved.
This is a Cisco internal test tool. It is not to be used outside of
Cisco except at the request of Cisco Engineering.
http://wwwin-routem.cisco.com
send feature request and questions to routem-support@cisco.com
ROUTEM:start reading config file  :Tue Mar 30 02:23:15 2021
(bgp:1 ospf:0 isis:0 bfd:0 tcp:0 msdp:0 traff:0)
ROUTEM:finish reading config file :Tue Mar 30 02:23:15 2021
```


## isp-1 router

BGP configuration on the `isp-1` router:

```
router bgp 65200
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 172.16.200.2 remote-as 65300
 neighbor 192.168.0.0 remote-as 65000
 neighbor 192.168.0.2 remote-as 65000
 !
 address-family ipv4
  network 172.16.0.0 mask 255.240.0.0
  neighbor 172.16.200.2 activate
  neighbor 192.168.0.0 activate
  neighbor 192.168.0.0 default-originate
  neighbor 192.168.0.2 activate
  neighbor 192.168.0.2 default-originate
 exit-address-family
```

Route summary before enabling routes with `routem`:

```
isp-1#show ip route summary 
IP routing table name is default (0x0)
IP routing table maximum-paths is 32
Route Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)
application     0           0           0           0           0
connected       0           7           0           672         2184
static          1           0           0           96          312
bgp 65200       1           0           0           96          312
  External: 1 Internal: 0 Local: 0
internal        2                                               1544
Total           4           7           0           864         4352
```

Route summar after injecting routes with `routem`:

```
isp-1#show ip route summary 
IP routing table name is default (0x0)
IP routing table maximum-paths is 32
Route Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)
application     0           0           0           0           0
connected       0           7           0           672         2184
static          1           0           0           96          312
bgp 65200       1           1500        0           144096      468312
  External: 1501 Internal: 0 Local: 0
internal        5                                               122440
Total           7           1507        0           144864      593248
```

First 50 routes from the BGP RIB (eBGP session):

```
isp-1#show ip bgp 
BGP table version is 10506, local router ID is 172.16.100.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
              t secondary path, L long-lived-stale,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
      0.0.0.0          0.0.0.0                                0 i
 *>   1.0.0.0/24       172.16.200.2                           0 65300 28874 52405 53333 60186 i
 *>   1.0.1.0/24       172.16.200.2                           0 65300 25293 i
 *>   1.0.2.0/24       172.16.200.2                           0 65300 21815 38535 33899 43069 i
 *>   1.0.3.0/24       172.16.200.2                           0 65300 36079 62643 i
 *>   1.0.4.0/24       172.16.200.2                           0 65300 43483 48421 13572 41743 i
 *>   1.0.5.0/24       172.16.200.2                           0 65300 19703 i
 *>   1.0.6.0/24       172.16.200.2                           0 65300 53681 i
 *>   1.0.7.0/24       172.16.200.2                           0 65300 29271 i
 *>   1.0.8.0/24       172.16.200.2                           0 65300 11587 i
 *>   1.0.9.0/24       172.16.200.2                           0 65300 18212 36050 55795 42086 i
 *>   1.0.10.0/24      172.16.200.2                           0 65300 43594 36738 i
 *>   1.0.11.0/24      172.16.200.2                           0 65300 63886 22708 i
 *>   1.0.12.0/24      172.16.200.2                           0 65300 36886 51606 29228 58968 i
 *>   1.0.13.0/24      172.16.200.2                           0 65300 26336 53895 i
 *>   1.0.14.0/24      172.16.200.2                           0 65300 9223 62467 36841 10210 i
 *>   1.0.15.0/24      172.16.200.2                           0 65300 45148 i
 *>   1.0.16.0/24      172.16.200.2                           0 65300 26120 8885 6213 32707 i
 *>   1.0.17.0/24      172.16.200.2                           0 65300 19425 i
 *>   1.0.18.0/24      172.16.200.2                           0 65300 59615 56510 21143 37674 i
 *>   1.0.19.0/24      172.16.200.2                           0 65300 51021 36026 i
 *>   1.0.20.0/24      172.16.200.2                           0 65300 37181 7378 31492 i
 *>   1.0.21.0/24      172.16.200.2                           0 65300 61346 48643 22210 49707 i
 *>   1.0.22.0/24      172.16.200.2                           0 65300 26432 46640 15048 i
 *>   1.0.23.0/24      172.16.200.2                           0 65300 58275 55195 i
 *>   1.0.24.0/24      172.16.200.2                           0 65300 18861 59080 i
 *>   1.0.25.0/24      172.16.200.2                           0 65300 46567 62899 i
 *>   1.0.26.0/24      172.16.200.2                           0 65300 44790 56980 31609 i
 *>   1.0.27.0/24      172.16.200.2                           0 65300 29119 54322 46419 60145 i
 *>   1.0.28.0/24      172.16.200.2                           0 65300 18065 62523 i
 *>   1.0.29.0/24      172.16.200.2                           0 65300 13939 58335 43808 31148 i
 *>   1.0.30.0/24      172.16.200.2                           0 65300 22014 52580 23612 i
 *>   1.0.31.0/24      172.16.200.2                           0 65300 18688 16353 i
 *>   1.0.32.0/24      172.16.200.2                           0 65300 38684 30213 i
 *>   1.0.33.0/24      172.16.200.2                           0 65300 59893 i
 *>   1.0.34.0/24      172.16.200.2                           0 65300 12632 i
 *>   1.0.35.0/24      172.16.200.2                           0 65300 51035 64611 i
 *>   1.0.36.0/24      172.16.200.2                           0 65300 46433 28196 50387 27317 i
 *>   1.0.37.0/24      172.16.200.2                           0 65300 19060 40382 i
 *>   1.0.38.0/24      172.16.200.2                           0 65300 14225 i
```


## edge-2 router

First 50 routes from `edge-2` router (iBGP session):

```
edge-2#show ip bgp 
BGP table version is 13839, local router ID is 192.168.200.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
              t secondary path, L long-lived-stale,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 * i  0.0.0.0          192.168.0.3              0    100      0 65200 i
 *>                    192.168.0.1                            0 65200 i
 * i  1.0.0.0/24       192.168.0.3              0    100      0 65200 65300 28874 52405 53333 60186 i
 *>                    192.168.0.1                            0 65200 65300 28874 52405 53333 60186 i
 * i  1.0.1.0/24       192.168.0.3              0    100      0 65200 65300 25293 i
 *>                    192.168.0.1                            0 65200 65300 25293 i
 * i  1.0.2.0/24       192.168.0.3              0    100      0 65200 65300 21815 38535 33899 43069 i
 *>                    192.168.0.1                            0 65200 65300 21815 38535 33899 43069 i
 * i  1.0.3.0/24       192.168.0.3              0    100      0 65200 65300 36079 62643 i
 *>                    192.168.0.1                            0 65200 65300 36079 62643 i
 * i  1.0.4.0/24       192.168.0.3              0    100      0 65200 65300 43483 48421 13572 41743 i
 *>                    192.168.0.1                            0 65200 65300 43483 48421 13572 41743 i
 * i  1.0.5.0/24       192.168.0.3              0    100      0 65200 65300 19703 i
 *>                    192.168.0.1                            0 65200 65300 19703 i
 * i  1.0.6.0/24       192.168.0.3              0    100      0 65200 65300 53681 i
 *>                    192.168.0.1                            0 65200 65300 53681 i
 * i  1.0.7.0/24       192.168.0.3              0    100      0 65200 65300 29271 i
 *>                    192.168.0.1                            0 65200 65300 29271 i
 * i  1.0.8.0/24       192.168.0.3              0    100      0 65200 65300 11587 i
 *>                    192.168.0.1                            0 65200 65300 11587 i
 * i  1.0.9.0/24       192.168.0.3              0    100      0 65200 65300 18212 36050 55795 42086 i
 *>                    192.168.0.1                            0 65200 65300 18212 36050 55795 42086 i
 * i  1.0.10.0/24      192.168.0.3              0    100      0 65200 65300 43594 36738 i
 *>                    192.168.0.1                            0 65200 65300 43594 36738 i
 * i  1.0.11.0/24      192.168.0.3              0    100      0 65200 65300 63886 22708 i
 *>                    192.168.0.1                            0 65200 65300 63886 22708 i
 * i  1.0.12.0/24      192.168.0.3              0    100      0 65200 65300 36886 51606 29228 58968 i
 *>                    192.168.0.1                            0 65200 65300 36886 51606 29228 58968 i
 * i  1.0.13.0/24      192.168.0.3              0    100      0 65200 65300 26336 53895 i
 *>                    192.168.0.1                            0 65200 65300 26336 53895 i
 * i  1.0.14.0/24      192.168.0.3              0    100      0 65200 65300 9223 62467 36841 10210 i
 *>                    192.168.0.1                            0 65200 65300 9223 62467 36841 10210 i
 * i  1.0.15.0/24      192.168.0.3              0    100      0 65200 65300 45148 i
 *>                    192.168.0.1                            0 65200 65300 45148 i
 * i  1.0.16.0/24      192.168.0.3              0    100      0 65200 65300 26120 8885 6213 32707 i
 *>                    192.168.0.1                            0 65200 65300 26120 8885 6213 32707 i
```