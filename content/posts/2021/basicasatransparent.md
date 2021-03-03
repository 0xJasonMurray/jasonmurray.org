---
title: "Overview and basic configuration for the Cisco ASA running in transparent mode."
date: 2021-03-03T06:31:00-06:00
toc: false
images:
categories:
  - tech
tags: 
  - asa
  - cisco
  - networking
  - transparent
  - firewall
description: Overview and basic configuration for the Cisco ASA running in transparent mode.
---

## Overview

Traditionally, a firewall is a routed hop and acts as a default gateway for hosts connected to the protected networks. A transparent firewall, on the other hand, is a Layer 2 firewall that acts like a “bump in the wire,” or a “stealth firewall,” and is not seen as a routed hop to connected devices. However, security policy, NAT, and most other security functions are the same as any other firewall.

Layer 2 connectivity is achieved by using a "bridge group" where you group together the inside and outside interfaces for a network, which allows traffic to pass between the "bridged" interfaces. Since the firewall is a "bump in the wire", another router is required to function as the Layer 3 router for client or point-to-point networks.

Transparent firewalls are commonly sandwiched between two routers.  The configuration and examples in this lab follow the topology diagram below:

[![Image of ](/images/2021-03-02-21-34-07.png)](/images/2021-03-02-21-34-07.png)


## Details

A production ready ASA configuration can be quite complex. The following guide touches on the basic steps required to enable a Cisco ASA as a transparent firewall based on the network diagram above.

First enable `transparent` mode:

```
firewall transparent
```

Transparent firewalls enforce network policy between devices by connecting two interfaces together through a layer 2 bridge. Start by creating a `bridge virtual interface` with an IP address that exists within the Layer 3 interfaces bridged together.  The `fusion-outside` router interface is 192.168.0.50 and `fusion-inside` is 192.168.0.49, the next available IP address is assigned to the `BVI`:

```
interface BVI1
 ip address 192.168.0.51 255.255.255.240 
```

The `BVI` interface connects physical interfaces to create the layer 2 path through the ASA. Add the `BVI` to each physical interface. In addition, set the `nameif` to a descriptive name, these names will be used later in the configuration.   Finally, set the `security-level`.  Traffic moving from a larger to a smaller number is automatically allowed.  Traffic moving from a smaller to larger number requires an `access policy` in order to communicate:

```
interface GigabitEthernet0/0
 bridge-group 1
 nameif inside
 security-level 90

interface GigabitEthernet0/1
 bridge-group 1
 nameif outside
 security-level 10
```

As noted above, communication from a smaller `security-level` to a larger one is blocked by default. To allow packets from the outside to the inside interface, a security policy must be created. In this example, we allow `icmp` as a way to test communication:

```
access-list acl_outside extended permit icmp any any
access-group acl_outside in interface outside
```

Enable logging to diagnose connection or policy issues:

```
logging enable 
logging buffered debugging
logging timestamp
```

The full configuration for a transparent Cisco ASA:

```
firewall transparent

interface GigabitEthernet0/0
 bridge-group 1
 nameif inside
 security-level 90

interface GigabitEthernet0/1
 bridge-group 1
 nameif outside
 security-level 10
            
interface BVI1
 ip address 192.168.0.51 255.255.255.240 

access-list acl_outside extended permit icmp any any
access-group acl_outside in interface outside

logging enable 
logging buffered debugging
logging timestamp
```

From the fusion router validate ICMP is allowed through:

```
fusion-outside#ping 192.168.0.49
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.49, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/4 ms
```

Validate the ASA is logging connections:

```
Mar 03 2021 03:36:33: %ASA-7-609001: Built local-host outside:192.168.0.50
Mar 03 2021 03:36:33: %ASA-7-609001: Built local-host inside:192.168.0.49
Mar 03 2021 03:36:33: %ASA-6-302020: Built inbound ICMP connection for faddr 192.168.0.50/12 gaddr 192.168.0.49/0 laddr 192.168.0.49/0 type 8 code 0 
Mar 03 2021 03:36:33: %ASA-6-302020: Built outbound ICMP connection for faddr 192.168.0.50/12 gaddr 192.168.0.49/0 laddr 192.168.0.49/0 type 0 code 0 
Mar 03 2021 03:36:35: %ASA-6-302021: Teardown ICMP connection for faddr 192.168.0.50/12 gaddr 192.168.0.49/0 laddr 192.168.0.49/0 type 8 code 0 
Mar 03 2021 03:36:35: %ASA-6-302021: Teardown ICMP connection for faddr 192.168.0.50/12 gaddr 192.168.0.49/0 laddr 192.168.0.49/0 type 0 code 0 
Mar 03 2021 03:36:35: %ASA-7-609002: Teardown local-host inside:192.168.0.49 duration 0:00:02
Mar 03 2021 03:36:35: %ASA-7-609002: Teardown local-host outside:192.168.0.50 duration 0:00:02
```

Validate the firewall is denying traffic by telnetting from the outside router to the inside router:

```
fusion-outside#telnet 192.168.0.49
Trying 192.168.0.49 ... 
% Connection timed out; remote host not responding
```

ASA is `denying` and logging packets not explicitly allowed from the `outside` to `inside` interface:

```
Mar 03 2021 03:42:02: %ASA-4-106023: Deny tcp src outside:192.168.0.50/21979 dst inside:192.168.0.49/23 by access-group "acl_outside" [0x0, 0x0]
```