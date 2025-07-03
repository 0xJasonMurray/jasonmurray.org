---
title: "Enabling external connectivity in Cisco Modeling Labs with bridged networks running on an ESXi hypervisor"
date: 2021-03-01T13:33:12-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cml
  - cisco
  - networking
  - esxi
  - bridged
description: Configuring devices in Cisco Modeling Lab running on ESXi with an external connector to reach networks outside the lab environment.
---

## Overview

Network lab environments are usually isolated from the production networks either by air-gap or a routed bastion host with legs into both sides. Some situations require lab devices to directly access external services. In the Cisco Modeling Lab environment, this is where an `external connector` is used. The `external connector` will `nat`, `bridge`, or `custom` connect the lab network to the production network.

In this post, we will discuss connecting the CML environment to the production network through a `bridged` link.  A bridged network is the equivalent of plugging a CML `router` directly into the same network as the CML management interface.  In this example, the home network router. A `csr1000v` would have an IP address on the same network as my laptop, Google home, or TV.

To use an `external connector`, setup the lab environment similar to the diagram below. Devices should not connect directly to the `external connector` because there is only a single interface. Use an `unmanaged switch` to connect multiple routers or devices (in this example there is only one router for simplicity):

[![Image of ](/images/2021-03-01-13-57-54.png)](/images/2021-03-01-13-57-54.png)


## Details

The Cisco `csr1000v` interface connected to the switch is configured to use DHCP:

```
interface GigabitEthernet1
 ip address dhcp
 negotiation auto
 no mop enabled
 no mop sysid
end
```

Validate the interface is enabled and setup to use DHCP:

```
Router#show ip interface brief 
Any interface listed with OK? value "NO" does not have a valid configuration

Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       unassigned      YES DHCP   up                    up      
```

If the ESXi server `vSwitch` does not have the proper security settings, the CML devices will send DHCP `discover` packets that fail to communicate with the `bridged` network.  This behavior is confirmed by debugging DHCP:

```
Router#debug dhcp              
DHCP client activity debugging is on
Router#%Unknown DHCP problem.. No allocation possible
*Mar  1 19:49:17.358: DHCP: Waiting for 35 seconds on interface GigabitEthernet1
*Mar  1 19:49:47.842: DHCP: deleting entry 7FF408C17D38 0.0.0.0 from list
*Mar  1 19:49:47.842: DHCP: Client socket is closed
*Mar  1 19:49:52.358: DHCP: Try 8 to acquire address for GigabitEthernet1
*Mar  1 19:49:52.370: DHCP: allocate request
*Mar  1 19:49:52.370: DHCP: new entry. add to queue, interface GigabitEthernet1
*Mar  1 19:49:52.370: DHCP: MAC address specified as  0000.0000.0000 (0 0). Xid is D19
*Mar  1 19:49:52.370: DHCP: SDiscover attempt # 1 for entry:
*Mar  1 19:49:52.370: DHCP: SDiscover: sending 303 byte length DHCP packet
*Mar  1 19:49:52.371: DHCP: SDiscover 303 bytes 
*Mar  1 19:49:52.371:             B'cast on GigabitEthernet1 interface from 0.0.0.0
*Mar  1 19:49:55.844: DHCP: SDiscover attempt # 2 for entry:
*Mar  1 19:49:55.844: DHCP: SDiscover: sending 303 byte length DHCP packet
*Mar  1 19:49:55.844: DHCP: SDiscover 303 bytes 
*Mar  1 19:49:55.844:             B'cast on GigabitEthernet1 interface from 0.0.0.0
*Mar  1 19:49:59.844: DHCP: SDiscover attempt # 3 for entry:
*Mar  1 19:49:59.844: DHCP: SDiscover: sending 303 byte length DHCP packet
*Mar  1 19:49:59.844: DHCP: SDiscover 303 bytes 
*Mar  1 19:49:59.845:             B'cast on GigabitEthernet1 interface from 0.0.0.0%Unknown DHCP problem.. No allocation possible
*Mar  1 19:50:12.409: DHCP: Waiting for 40 seconds on interface GigabitEthernet1
```

If packets fail to egress the `external connector`, it may be necessary to change the security settings on the ESXi `vSwitch`.  Enter the security setting by navigating to `Networking -> vSwitch0 -> Edit Settings -> Security`.  Change `Promiscuous Mode` and `Forged Transmits` to `Accept`:

[![Image of ](/images/2021-03-01-13-50-40.png)](/images/2021-03-01-13-50-40.png)

Once the `vSwitch` is configured properly, the DHCP process succeeds.  A `discover` promptly receives a `offer`:

```
*Mar  1 19:50:52.421: DHCP: SDiscover 303 bytes 
*Mar  1 19:50:52.421:             B'cast on GigabitEthernet1 interface from 0.0.0.0
*Mar  1 19:50:53.264: DHCP: Received a BOOTREP pkt
*Mar  1 19:50:53.264: DHCP: offer received from 192.168.86.1
*Mar  1 19:50:53.264: DHCP: SRequest attempt # 1 for entry:
*Mar  1 19:50:53.264: DHCP: SRequest- Server ID option: 192.168.86.1
*Mar  1 19:50:53.264: DHCP: SRequest- Requested IP addr option: 192.168.86.41
*Mar  1 19:50:53.265: DHCP: SRequest placed lease len option: 86400
*Mar  1 19:50:53.265: DHCP: SRequest: 321 bytes
*Mar  1 19:50:53.265: DHCP: SRequest: 321 bytes
*Mar  1 19:50:53.265:             B'cast on GigabitEthernet1 interface from 0.0.0.0
*Mar  1 19:50:53.284: DHCP: Received a BOOTREP pkt
*Mar  1 19:50:57.296: DHCP: Sending notification of ASSIGNMENT:
*Mar  1 19:50:57.296:   Address 192.168.86.41 mask 255.255.255.0
*Mar  1 19:50:57.296: DHCP Client Pooling: ***Allocated IP address: 192.168.86.41
*Mar  1 19:50:57.305: Allocated IP address = 192.168.86.41  255.255.255.0

*Mar  1 19:50:57.305: %DHCP-6-ADDRESS_ASSIGN: Interface GigabitEthernet1 assigned DHCP address 192.168.86.41, mask 255.255.255.0, hostname Router
```

Validate the interface has an address:

```
Router#show ip interface brief
Any interface listed with OK? value "NO" does not have a valid configuration

Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       192.168.86.41   YES DHCP   up                    up      
```

Validate the router can ping devices on the public Internet:

```
Router#ping 8.8.8.8
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/18/39 ms
```


## Best Practice for Labs

In a lab environment, connecting the `global routing table` to an `external connector` may affect internal routes.  For example, DHCP installs a default route in the main routing table.  You probably don't want this in your lab:

```
S*    0.0.0.0/0 [254/0] via 192.168.86.1
      192.168.86.0/24 is variably subnetted, 3 subnets, 2 masks
C        192.168.86.0/24 is directly connected, GigabitEthernet1
S        192.168.86.1/32 [254/0] via 192.168.86.1, GigabitEthernet1
L        192.168.86.41/32 is directly connected, GigabitEthernet1
```

To eliminated the external network from muddying up the lab routing table, use a `management VRF` to isolate the `external connector` network.  

Create a management `vrf`:

```
vrf definition management
 !
 address-family ipv4
 exit-address-family
```

Add the interface connected to the `external connector` to the `management vrf`:

```
interface GigabitEthernet1
 vrf forwarding management
 ip address dhcp
 negotiation auto
 no mop enabled
 no mop sysid
end
```

Validate the the interface receives an IP address:

```
Router#show ip interface brief 
Any interface listed with OK? value "NO" does not have a valid configuration

Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet1       192.168.86.41   YES DHCP   up                    up      
```

When the `Gi1` interface is in the `management` VRF, the global routing table has no entries:

```
Router#show ip route
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

Gateway of last resort is not set
```

The `management VRF` now contains the default route:

```
Router#show ip route vrf management

Routing Table: management
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

Gateway of last resort is 192.168.86.1 to network 0.0.0.0

S*    0.0.0.0/0 [254/0] via 192.168.86.1
      192.168.86.0/24 is variably subnetted, 3 subnets, 2 masks
C        192.168.86.0/24 is directly connected, GigabitEthernet1
S        192.168.86.1/32 [254/0] via 192.168.86.1, GigabitEthernet1
L        192.168.86.41/32 is directly connected, GigabitEthernet1
```


Ping no longer works:

```
Router#ping 8.8.8.8
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

The `vrf` keyword must be used for all commands that require external access:

```
Router#ping vrf management 8.8.8.8
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/10/11 ms
```