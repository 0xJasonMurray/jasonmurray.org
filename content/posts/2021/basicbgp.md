---
title: "Basic BGP Cisco Lab Configuration"
date: 2021-02-27T10:22:26-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - bgp
  - cml
  - networking
description: Cisco Modeling Lab utilizing `is-is` to exchange internal point-to-point and loopback interfaces routes, `ebgp` to exchange external routes between organizations, and `ibgp` to exchange the external routes learned from `ebgp` to internal routers.
---

## Overview

Building on the [is-is basic lab](/posts/2021/basicisis/), the next component of network routing design involves connecting one organization to another.  Whereas, `is-is` is used to exchange infrastructure routes internally, the BGP protocol is designed to communicate routes with external entities.

This lab utilizes `is-is` to exchange internal point-to-point and loopback interfaces routes, `ebgp` to exchange external routes between organizations, and `ibgp` to exchange the external routes learned from `ebgp` to internal routers.

Download the [lab yaml file here](/misc/basicbgplab.yaml)

## Lab Topology Diagrams

Cisco Modeling Lab topology:

[![Image of basic bgp topology](/images/2021-02-25-10-23-41.png)](/images/2021-02-25-10-23-41.png)

Logical topology with organization, name and addressing information:

[![Image of ](/images/2021-02-25-12-55-11.png)](/images/2021-02-25-12-55-11.png)

## Template Configurations

Inter AS router configuration template for eBGP routers.

- `ip route 192.168.0.0 255.255.128.0 Null0` - It's common practice for BGP to exchange a summary of the organizations network prefixes, not each individual route. To exchange a network prefix with `bgp`, the exact route must exist in the FIB (routing table).  To meet this requirement, a static route with the summary prefix is manually added as an `anchor route`.
- `network 192.168.0.0 mask 255.255.128.0` - Network prefix to exchange with the neighbors.

```text
ip route 192.168.0.0 255.255.128.0 Null0

router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.5 remote-as 65100
 neighbor 192.168.0.7 remote-as 65100
 !
 address-family ipv4
  network 192.168.0.0 mask 255.255.128.0
  neighbor 192.168.0.5 activate
  neighbor 192.168.0.7 activate
 exit-address-family
```

Local router configuration template for iBGP routers:

```text
router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.8 remote-as 65000
 neighbor 192.168.0.2 remote-as 65000
 !
 address-family ipv4
  neighbor 192.168.0.8 activate
  neighbor 192.168.0.2 activate
 exit-address-family
```

## Router Configurations

### csr1000v-0

Router configuration:

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

### csr1000v-1

Router configuration:

```text
ip route 192.168.0.0 255.255.128.0 Null0

router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.0 remote-as 65000
 neighbor 192.168.0.5 remote-as 65100
 neighbor 192.168.0.9 remote-as 65000
 !
 address-family ipv4
  network 192.168.0.0 mask 255.255.128.0
  neighbor 192.168.0.0 activate
  neighbor 192.168.0.5 activate
  neighbor 192.168.0.9 activate
 exit-address-family
```

### csr1000v-2

Router configuration:

```text
ip route 192.168.0.0 255.255.128.0 Null0

router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.2 remote-as 65000
 neighbor 192.168.0.7 remote-as 65100
 neighbor 192.168.0.8 remote-as 65000
 !
 address-family ipv4
  network 192.168.0.0 mask 255.255.128.0
  neighbor 192.168.0.2 activate
  neighbor 192.168.0.7 activate
  neighbor 192.168.0.8 activate
 exit-address-family
```


### csr1000v-3

Router configuration:

```text
ip route 192.168.192.0 255.255.224.0 Null0

router bgp 65100
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.4 remote-as 65000
 neighbor 192.168.0.6 remote-as 65000
 !
 address-family ipv4
  network 192.168.192.0 mask 255.255.224.0
  neighbor 192.168.0.4 activate
  neighbor 192.168.0.4 default-originate
  neighbor 192.168.0.6 activate
  neighbor 192.168.0.6 default-originate
 exit-address-family
```

## Testing and Validation

Validate internal and external BGP neighbors from the `csr1000v-1` which is connected to both the internal and external organizations:

```
csr1000v-1#show ip bgp summary 
BGP router identifier 192.168.100.1, local AS number 65000
BGP table version is 9, main routing table version 9
3 network entries using 744 bytes of memory
6 path entries using 864 bytes of memory
5/3 BGP path/bestpath attribute entries using 1440 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 3072 total bytes of memory
BGP activity 6/3 prefixes, 10/4 paths, scan interval 60 secs
3 networks peaked at 17:03:10 Feb 25 2021 UTC (00:07:25.869 ago)

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.168.0.0     4        65000      29      30        9    0    0 00:22:43        0
192.168.0.5     4        65100      34      29        9    0    0 00:22:45        2
192.168.0.9     4        65000      24      24        9    0    0 00:15:28        3
```

Validate iBGP neighbors on the `csr1000v-0` router.  This router is only connected to internal routers:

```
csr1000v-0#show ip bgp summary 
BGP router identifier 192.168.100.0, local AS number 65000
BGP table version is 11, main routing table version 11
3 network entries using 744 bytes of memory
6 path entries using 864 bytes of memory
2/2 BGP path/bestpath attribute entries using 576 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 2208 total bytes of memory
BGP activity 4/1 prefixes, 10/4 paths, scan interval 60 secs
3 networks peaked at 17:03:10 Feb 25 2021 UTC (00:08:01.442 ago)

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.168.0.1     4        65000      31      29       11    0    0 00:23:18        3
192.168.0.3     4        65000      42      40       11    0    0 00:31:46        3
```

Validate full routing table on `csr1000v-0`.  This router has prefixes from connected interfaces, internal networks, and external networks:

```
csr1000v-0#show ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, m - OMP
       n - NAT, Ni - NAT inside, No - NAT outside, Nd - NAT DIA
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       H - NHRP, G - NHRP registered, g - NHRP registration summary
       o - ODR, P - periodic downloaded static route, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is 192.168.0.5 to network 0.0.0.0

B*    0.0.0.0/0 [200/0] via 192.168.0.5, 00:22:31
B     192.168.0.0/17 [200/0] via 192.168.0.1, 00:07:10
      192.168.0.0/24 is variably subnetted, 7 subnets, 2 masks
C        192.168.0.0/31 is directly connected, GigabitEthernet1
L        192.168.0.0/32 is directly connected, GigabitEthernet1
C        192.168.0.2/31 is directly connected, GigabitEthernet2
L        192.168.0.2/32 is directly connected, GigabitEthernet2
i L2     192.168.0.4/31 [115/20] via 192.168.0.1, 14:21:05, GigabitEthernet1
i L2     192.168.0.6/31 [115/20] via 192.168.0.3, 14:20:31, GigabitEthernet2
i L2     192.168.0.8/31 [115/20] via 192.168.0.3, 00:16:34, GigabitEthernet2
                        [115/20] via 192.168.0.1, 00:16:34, GigabitEthernet1
      192.168.100.0/32 is subnetted, 3 subnets
C        192.168.100.0 is directly connected, Loopback0
i L2     192.168.100.1 [115/20] via 192.168.0.1, 14:17:34, GigabitEthernet1
i L2     192.168.100.2 [115/20] via 192.168.0.3, 14:20:31, GigabitEthernet2
B     192.168.192.0/19 [200/0] via 192.168.0.5, 00:08:22
```

Validate a complete path is working as expected from farthest router to server:

```
csr1000v-0#traceroute 192.168.200.2
Type escape sequence to abort.
Tracing the route to 192.168.200.2
VRF info: (vrf in name/id, vrf out name/id)
  1 192.168.0.1 3 msec 1 msec 1 msec
  2 192.168.0.5 2 msec 2 msec 2 msec
  3 192.168.200.2 [AS 65100] 3 msec 3 msec 2 msec
```

Validate server reachability:

```
csr1000v-0#ping 192.168.200.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.200.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
```

