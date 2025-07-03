---
title: "Helpful commands when troubleshooting IS-IS on Cisco routers"
date: 2020-06-03T08:58:31-05:00
toc: false
images:
categories:
  - tech
tags:
  - networking
  - is-is
  - igp
---

Validate IS-IS is running on an interface:

```
iosv-1#show isis protocol

Tag 65010:
IS-IS Router: 65010
  System Id: 1921.6800.0001.00  IS-Type: level-1-2
  Manual area address(es):
	49
  Routing for area address(es):
	49
  Interfaces supported by IS-IS:
	GigabitEthernet0/1 - IP
	GigabitEthernet0/2 - IP
	Loopback0 - IP
```

Show IS-IS neighbor adjacency:

```
iosv-1#show isis neighbors

Tag 65010:
System Id       Type Interface     IP Address      State Holdtime Circuit Id
iosv-2          L2   Gi0/2         10.0.0.6        UP    27       01
```


Find a specific IS-IS prefix entry:

```
router#show isis ip rib 0.0.0.0 0.0.0.0


IPv4 local RIB for IS-IS process

IPV4 unicast topology base (TID 0, TOPOID 0x0) =================

0.0.0.0/0
  [175/L2/2500] via 192.168.161.130(TenGigabitEthernet2/1/3), from 192.168.247.156, tag 0, LSP[16/29069]
  [175/L2/2500] via 192.168.161.128(TenGigabitEthernet1/1/3), from 192.168.247.156, tag 0, LSP[16/29069]
  [175/L2/2500] via 192.168.161.108(TenGigabitEthernet1/1/4), from 192.168.247.156, tag 0, LSP[16/29069]
  [175/L2/2500] via 192.168.161.110(TenGigabitEthernet2/1/4), from 192.168.247.156, tag 0, LSP[16/29069]
```


Dump the complete IS-IS routing information base (RIB):

```
iosv-1#show isis ip rib

IPv4 local RIB for IS-IS process 65010

IPV4 unicast topology base (TID 0, TOPOID 0x0) =================

10.0.0.4/30  prefix attr X:0 R:0 N:0
  [115/L2/2] via 10.0.0.6(GigabitEthernet0/2), from 192.168.0.3, tag 0, LSP[3/3/397]
     prefix attr: X:0 R:0 N:0

192.168.0.3/32  prefix attr X:0 R:0 N:1
  [115/L2/11] via 10.0.0.6(GigabitEthernet0/2), from 192.168.0.3, tag 0, LSP[3/3/397]
     prefix attr: X:0 R:0 N:1
              (installed)
```

Display routes installed into the FIB from IS-IS:

```
iosv-1#show ip route isis
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      192.168.0.0/32 is subnetted, 4 subnets
i L2     192.168.0.3 [115/11] via 10.0.0.6, 4d03h, GigabitEthernet0/2
```

On NX-OS the IS-IS process can be restarted if it hangs:

```
restart isis <processname>
```
