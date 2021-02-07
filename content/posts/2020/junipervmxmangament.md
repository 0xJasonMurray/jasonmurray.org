---
title: "Out-of-band routing table separation in GNS3 on the Juniper vMX"
date: 2020-08-05T11:45:38-05:00
toc: false
images:
categories:
  - tech
tags: 
  - juniper
  - vmx
  - gns3
  - out-of-band
---



While GNS3 offers console access to individual devices, I prefer direct SSH access from my home network. This way GNS3 does not need to be open and I don't need to remember the console `telnet` ports.

However, mixing the out-of-band network and production (lab) networks may cause unexpected consequences such as routing conflicts or worse, peering a lab environment with a real production network.   To avoid these problems, it is best practice to isolate the global routing table from the out-of-band routing table through the use of VRFs or routing-instances.

In this example, a cloud router is provisioned to bridge the home network directly into a Juniper vMX:

![juniper oob gns3](/images/juniperoobgns3.png)

The Juniper configuration below will create a separate `routing-instance` for the management network, enable DHCP on the interface connected to the cloud router, and enable SSH for remote management:

```
set interfaces ge-0/0/9 unit 0 family inet dhcp
set routing-instances homenet instance-type virtual-router
set routing-instances homenet description "System Management"
set routing-instances homenet interface ge-0/0/9.0
set system services ssh
set system login user jemurray class super-user authentication plain-text-password
```

Unless the `homenet` routing-instance is selected, the global routing table does not have access outside the private lab environment:

```
jemurray@900w-lab> ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 0 packets received, 100% packet loss



jemurray@900w-lab> ping routing-instance homenet 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: icmp_seq=0 ttl=116 time=61.045 ms
64 bytes from 8.8.8.8: icmp_seq=1 ttl=116 time=68.797 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max/stddev = 61.045/64.921/68.797/3.876 ms
```

Confirm proper operation by looking at the routing tables: 

```
jemurray@900w-lab> show route

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
Restart Complete
+ = Active Route, - = Last Active, * = Both

0.0.0.0/0          *[Static/5] 00:24:35
                       Discard
10.6.6.6/32        *[Static/5] 00:24:35
                       Discard
10.224.0.0/12      *[Static/5] 00:24:35
                       Discard
38.104.162.76/30   *[Direct/0] 00:23:43
                    >  via ge-0/0/6.0
38.104.162.78/32   *[Local/0] 00:23:43
                       Local via ge-0/0/6.0
192.168.0.0/16     *[Static/5] 00:24:35
                       Discard
192.168.2.225/32   *[Direct/0] 00:24:36
                    >  via lo0.0
192.168.7.0/24     *[Static/5] 00:24:35
                       Discard
192.168.100.124/30 *[Direct/0] 00:23:43
                    >  via ge-0/0/4.0
192.168.100.126/32 *[Local/0] 00:23:43
                       Local via ge-0/0/4.0
192.168.182.0/24   *[Static/5] 00:24:35
                       Discard
172.16.0.0/12      *[Static/5] 00:24:35
                       Discard
192.31.46.0/24     *[Static/5] 00:24:35
                       Discard

homenet.inet.0: 3 destinations, 3 routes (3 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

0.0.0.0/0          *[Access-internal/12] 00:23:35, metric 0
                    >  to 192.168.86.1 via ge-0/0/9.0
192.168.86.0/24    *[Direct/0] 00:23:36
                    >  via ge-0/0/9.0
192.168.86.46/32   *[Local/0] 00:23:36
                       Local via ge-0/0/9.0
```

