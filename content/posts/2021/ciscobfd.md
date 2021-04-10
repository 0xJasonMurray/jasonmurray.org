---
title: "Using BFD to reduce BGP convergence"
date: 2021-04-10T13:53:03-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - networking
  - bfd
  - tcl
description: By enabling `bfd` on an eBGP session, convergence (route withdrawl) on a failed link is reduced from 150 seconds to less then 1 second.
---

## Overview

Given the following topology:

[![Image of bfd network topology ](/images/2021-04-10-13-57-35.png)](/images/2021-04-10-13-57-35.png)

By enabling `bfd` on an eBGP session between the `edge-2` and `isp-1` routers, BGP convergence (route withdrawl) on a failed link is reduced from 150 seconds to less then 1 second.

## Details

To automate counting the number of seconds before the routes are withdrawn, I run a `tcl` script (because Cisco only supports TCL scripting even though it is 2021) on the router. Run the script by typing `tclsh` and pasting the script below:

```text
set x 0
while "1" {
    set x [expr {$x + 1}]
    puts "x is $x"
    set results [show ip route 1.1.243.1]
    puts "$results"
    after 1000
}
```

### Configuration

Enable BFD on the edge-2 interface to isp-1. Note, BFD must be configured on a BGP neighbor (see below) before it will establish an active session with the neighbor:

```text
interface GigabitEthernet4
 description to isp1 gi1
 ip address 192.168.0.0 255.255.255.254
 negotiation auto
 bfd interval 50 min_rx 50 multiplier 5
 no mop enabled
 no mop sysid
end
```

Enable BFD in the BGP instance on edge-2:

```text
router bgp 65000
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 192.168.0.1 remote-as 65200
 neighbor 192.168.0.1 fall-over bfd
 !
 address-family ipv4
  network 192.168.0.0 mask 255.255.0.0
  neighbor 192.168.0.1 activate
 exit-address-family
```

Enable BFD on the isp-1 interface to edge-2. Note, BFD must be configured on a BGP neighbor (see below) before it will establish an active session with the neighbor:

```text
interface GigabitEthernet1
 description to GigabitEthernet4.edge-2
 ip address 192.168.0.1 255.255.255.254
 shutdown
 negotiation auto
 bfd interval 50 min_rx 50 multiplier 5
 no mop enabled
 no mop sysid
end
```

Enable BFD in the BGP instance on isp-1:

```text
router bgp 65200
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 neighbor 172.16.200.2 remote-as 65300
 neighbor 192.168.0.0 remote-as 65000
 neighbor 192.168.0.0 fall-over bfd
 !
 address-family ipv4
  network 172.16.0.0 mask 255.240.0.0
  neighbor 172.16.200.2 activate
  neighbor 192.168.0.0 activate
  neighbor 192.168.0.0 default-originate
 exit-address-family
```

### Testing / Validation

Validate a `bfd` session is established between the routers:

```
edge-2#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.168.0.1                          4097/4097       Up        Up        Gi4
```

Run the test script, then `shut` the `gi2` interface on isp-1:

```
edge-2#tclsh
edge-2(tcl)#set x 0
0
edge-2(tcl)#while "1" {
+>    set x [expr {$x + 1}]
+>    puts "x is $x"
+>    set results [show ip route 1.1.243.1]
+>    puts "$results"
+>    after 1000
+>}
x is 1

Routing entry for 1.1.243.0/24
  Known via "bgp 65000", distance 200, metric 0
  Tag 65200, type internal
  Last update from 192.168.0.3 00:00:03 ago
  Routing Descriptor Blocks:
  * 192.168.0.3, from 192.168.200.1, 00:00:03 ago
      Route metric is 0, traffic share count is 1
      AS Hops 5
      Route tag 65200
      MPLS label: none
x is 2

% Network not in table
```

### Summary

In a production environments run `bfd` on all links.