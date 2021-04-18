---
title: "Enable global anycast interface on an Infoblox DNS server"
date: 2021-04-18T14:27:32-05:00
draft: true
toc: false
images:
categories:
  - tech
tags: 
  - infoblox
  - anycast
  - ospf
description:
---

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

Gateway of last resort is 192.168.86.1 to network 0.0.0.0

S*    0.0.0.0/0 [254/0] via 192.168.86.1
      192.168.0.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.0.0/31 is directly connected, GigabitEthernet2
L        192.168.0.0/32 is directly connected, GigabitEthernet2
      192.168.86.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.86.0/24 is directly connected, GigabitEthernet1
L        192.168.86.24/32 is directly connected, GigabitEthernet1
```

```
csr1000v-0#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.0.1       1   FULL/DR         00:00:37    192.168.0.1     GigabitEthernet2
```

[![Image of ](/images/2021-04-16-15-42-53.png)](/images/2021-04-16-15-42-53.png)

[![Image of ](/images/2021-04-16-15-43-21.png)](/images/2021-04-16-15-43-21.png)

[![Image of ](/images/2021-04-16-15-44-32.png)](/images/2021-04-16-15-44-32.png)


