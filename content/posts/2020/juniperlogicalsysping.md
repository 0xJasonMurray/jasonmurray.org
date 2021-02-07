---
title: "Creating logical tunnels within logical systems on JunOS"
date: 2020-10-04T16:32:54-05:00
toc: false
images:
categories:
  - tech
tags: 
  - juniper
  - logicalsystems
  - logicaltunnels
---

# Overview

Logical systems are a feature within the JunOS operating system to create `virtual routers` within a single physical platform.  Virtualizing the routing platform allows users to maximize resources, separate work loads, or partition services without needing to purchase and install physically separate hardware.  

A virtual router has limited use if not connected to other routers. To connect multiple logical systems, there are two main options:

- Connect each instance with physical interfaces and cables.  (Note: On Cisco's VDCs this is the only option).
- Create logical tunnels which act as virtual interfaces between logical systems.

# Details

Create the logical systems and interfaces.  

- Tunnel interfaces are prefixed with: `lt-`. 
  - Note: check the JunOS guide, logical-tunnels require specific hardware support.  On the vMX platform, I had to use: `0/0/0`.  
- Logical tunnels use the same `Interface name`. A unique `unit` identifier is required on each virtual interface.
- Use the `peer-unit` option to connect interfaces together.

Example configuration:

```
jemurray@juniper-1> show configuration logical-systems
LogicalSystem-1 {
    interfaces {
        lt-0/0/0 {
            unit 101 {
                encapsulation ethernet;
                peer-unit 201;
                family inet {
                    address 192.168.99.1/24;
                }
            }
        }
    }
}
LogicalSystem-2 {
    interfaces {
        lt-0/0/0 {
            unit 201 {
                encapsulation ethernet;
                peer-unit 101;
                family inet {
                    address 192.168.99.2/24;
                }
            }
        }
    }
}
```

The interface configuration alone is not enough.  Trying to ping between interfaces will fail:

```
jemurray@juniper-1> ping 192.168.99.2 logical-system LogicalSystem-1
PING 192.168.99.2 (192.168.99.2): 56 data bytes
ping: sendto: Can't assign requested address
ping: sendto: Can't assign requested address
^C
--- 192.168.99.2 ping statistics ---
2 packets transmitted, 0 packets received, 100% packet loss
```

The `tunnel-services` option needs to be enabled on the `fpc` and `pic` with the proper bandwidth set:

```
jemurray@juniper-1# set chassis fpc 0 pic 0 tunnel-services bandwidth 10g

jemurray@juniper-1# show chassis
fpc 0 {
    pic 0 {
        tunnel-services {
            bandwidth 10g;
        }
    }
}
```

Communication is successful:

```
jemurray@juniper-1> ping 192.168.99.2 logical-system LogicalSystem-1
PING 192.168.99.2 (192.168.99.2): 56 data bytes
64 bytes from 192.168.99.2: icmp_seq=0 ttl=64 time=24.495 ms
64 bytes from 192.168.99.2: icmp_seq=1 ttl=64 time=6.981 ms
^C
--- 192.168.99.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max/stddev = 6.981/15.738/24.495/8.757 ms
```

# Other Notes

The vMX logical tunnel interface needs to be on interface 0/0/0 to work properly.  I tried a variety of other interfaces to see what happens:

```
[edit]
jemurray@juniper-1# rename logical-systems LogicalSystem-1 interfaces lt-0/0/0 to lt-0/0/7

[edit]
jemurray@juniper-1# rename logical-systems LogicalSystem-2 interfaces lt-0/0/0 to lt-0/0/7

[edit]
jemurray@juniper-1# commit
commit complete

[edit]
jemurray@juniper-1# exit
Exiting configuration mode

jemurray@juniper-1> ping 192.168.99.2 logical-system LogicalSystem-1
PING 192.168.99.2 (192.168.99.2): 56 data bytes
ping: sendto: Can't assign requested address
ping: sendto: Can't assign requested address
ping: sendto: Can't assign requested address
^C
--- 192.168.99.2 ping statistics ---
3 packets transmitted, 0 packets received, 100% packet loss
```

