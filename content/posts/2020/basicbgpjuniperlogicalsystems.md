---
title: "Basic BGP configuration with Juniper logical systems"
date: 2020-10-05T16:30:52-05:00
toc: false
images:
categories:
  - tech
tags: 
  - juniper
  - bgp
  - logicalsystems
---

# Overview

In this example, we provide the basic configuration for two Juniper vMX logical systems to exchange 6 eBGP routes with each other.

![BGP Network Topology](/images/juniper-ls-bgp-basics-net-diag.png)

Each major area of configuration is detailed with inline comments (#).


# The Configuration

Create the logical systems point-to-point interfaces:

```
# Logical System 1
# Each interface in a logical system needs a unique unit ID
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 description "LogicalSystem-2 P2P Interface"
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 encapsulation ethernet
# The unit ID is used to connect the logical tunnel interfaces
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 peer-unit 201
# A /31 is used to conserve IP space (yes, you can use .0 as a valid interface)
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 family inet address 192.168.99.0/31

# Logical System 2
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 description "LogicalSystem-1 P2P Interface"
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 encapsulation ethernet
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 peer-unit 101
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 family inet address 192.168.99.1/31
```

Create basic BGP peering:

```
# Logical System 1
# Create BGP groups when reusing common BGP configuration
# Create an external BGP relationship
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b type external
# Export ONLY the prefixes specified in the prefix-list.  This statement is REQUIRED, otherwise BGP won't export any routes.
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b export export-local-prefixes
# Establish a connection to the neighbor 
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b neighbor 192.168.99.1 peer-as 64513
# Our local ASN
set logical-systems LogicalSystem-1 routing-options autonomous-system 64512

# Logical System 2
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a type external
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a export export-local-prefixes
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a neighbor 192.168.99.0 peer-as 64512
set logical-systems LogicalSystem-2 routing-options autonomous-system 64513
```

Create static anchor routes.  BGP requires a valid route in the FIB (routing table) to export routes to a neighbor.

```
# Logical System 1
# It is best practice to install BGP routes that won't flap if 
# interfaces or routers go up and down.  This is accomplished 
# by creating static routes to the discard device.  These types 
# of routes are known as anchor routes.  Create static route 
# for every network to be exported by BGP.
set logical-systems LogicalSystem-1 routing-options static route 10.100.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.101.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.102.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.103.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.104.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.105.0.0/16 discard

# Logical System 2
set logical-systems LogicalSystem-2 routing-options static route 10.200.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.201.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.202.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.203.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.204.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.205.0.0/16 discard
```

Routes are not sent to neighbors automatically, they must be explicitly specified and match an entry in the FIB.  This is why static anchor routes are configured.

Create the export policy used to tell BGP what routes need to be sent.

```
# Logical System 1
# This is the prefix list attached to the export policy statement.
# It is best practice to create a prefix list that can be reused in 
# multiple places if necessary.
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.100.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.101.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.102.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.103.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.104.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.105.0.0/16

# The export policy that is used to control what routes are exported through BGP.
set logical-systems LogicalSystem-1 policy-options policy-statement export-local-prefixes term 1 from prefix-list local-prefixes
set logical-systems LogicalSystem-1 policy-options policy-statement export-local-prefixes term 1 then accept

# Logical System 2
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.200.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.201.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.202.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.203.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.204.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.205.0.0/16
set logical-systems LogicalSystem-2 policy-options policy-statement export-local-prefixes term 1 from prefix-list local-prefixes
set logical-systems LogicalSystem-2 policy-options policy-statement export-local-prefixes term 1 then accept
```

The entire configuration:

```
jemurray@juniper-1> show configuration | display set
set version 20190829.221548_builder.r1052644
set system host-name juniper-1
set system root-authentication encrypted-password "NotForThePublic"
set system login user jemurray uid 2000
set system login user jemurray class super-user
set system login user jemurray authentication encrypted-password "NotForThePublic"
set system services ssh
set system syslog user * any emergency
set system syslog file messages any notice
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands any
set system processes dhcp-service traceoptions file dhcp_logfile
set system processes dhcp-service traceoptions file size 10m
set system processes dhcp-service traceoptions level all
set system processes dhcp-service traceoptions flag packet
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 description "LogicalSystem-2 P2P Interface"
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 encapsulation ethernet
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 peer-unit 201
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 family inet address 192.168.99.0/31
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b type external
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b export export-local-prefixes
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b neighbor 192.168.99.1 peer-as 64513
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.100.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.101.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.102.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.103.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.104.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.105.0.0/16
set logical-systems LogicalSystem-1 policy-options policy-statement export-local-prefixes term 1 from prefix-list local-prefixes
set logical-systems LogicalSystem-1 policy-options policy-statement export-local-prefixes term 1 then accept
set logical-systems LogicalSystem-1 routing-options static route 10.100.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.101.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.102.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.103.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.104.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.105.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options autonomous-system 64512
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 description "LogicalSystem-1 P2P Interface"
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 encapsulation ethernet
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 peer-unit 101
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 family inet address 192.168.99.1/31
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a type external
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a export export-local-prefixes
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a neighbor 192.168.99.0 peer-as 64512
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.200.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.201.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.202.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.203.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.204.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.205.0.0/16
set logical-systems LogicalSystem-2 policy-options policy-statement export-local-prefixes term 1 from prefix-list local-prefixes
set logical-systems LogicalSystem-2 policy-options policy-statement export-local-prefixes term 1 then accept
set logical-systems LogicalSystem-2 routing-options static route 10.200.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.201.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.202.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.203.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.204.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.205.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options autonomous-system 64513
set chassis fpc 0 pic 0 tunnel-services bandwidth 10g
set interfaces ge-0/0/1 unit 0 family inet dhcp
set interfaces fxp0 unit 0 family inet dhcp vendor-id Juniper-vmx-VM5F52E462F8
```

# Validate BGP Operation

Validate BGP session is established:

```
jemurray@juniper-1> show bgp neighbor 192.168.99.1 logical-system LogicalSystem-1
Peer: 192.168.99.1+65463 AS 64513 Local: 192.168.99.0+179 AS 64512
  Group: remote-peers-group-b  Routing-Instance: master
  Forwarding routing-instance: master
  Type: External    State: Established    Flags: <Sync>
  Last State: OpenConfirm   Last Event: RecvKeepAlive
  Last Error: None
  Export: [ export-local-prefixes ]
  Options: <Preference PeerAS Refresh>
  Options: <GracefulShutdownRcv>
  Holdtime: 90 Preference: 170
  Graceful Shutdown Receiver local-preference: 0
  Number of flaps: 0
  Peer ID: 192.168.99.1    Local ID: 192.168.99.0      Active Holdtime: 90
  Keepalive Interval: 30         Group index: 0    Peer index: 0    SNMP index: 0
  I/O Session Thread: bgpio-0 State: Enabled
  BFD: disabled, down
  Local Interface: lt-0/0/0.101
  NLRI for restart configured on peer: inet-unicast
  NLRI advertised by peer: inet-unicast
  NLRI for this session: inet-unicast
  Peer supports Refresh capability (2)
  Stale routes from peer are kept for: 300
  Peer does not support Restarter functionality
  Restart flag received from the peer: Notification
  NLRI that restart is negotiated for: inet-unicast
  NLRI of received end-of-rib markers: inet-unicast
  NLRI of all end-of-rib markers sent: inet-unicast
  Peer does not support LLGR Restarter functionality
  Peer supports 4 byte AS extension (peer-as 64513)
  Peer does not support Addpath
  NLRI(s) enabled for color nexthop resolution: inet-unicast
  Table inet.0 Bit: 20000
    RIB State: BGP restart is complete
    Send state: in sync
    Active prefixes:              6
    Received prefixes:            6
    Accepted prefixes:            6
    Suppressed due to damping:    0
    Advertised prefixes:          6
  Last traffic (seconds): Received 3    Sent 3    Checked 3755
  Input messages:  Total 143	Updates 2	Refreshes 0	Octets 2807
  Output messages: Total 142	Updates 1	Refreshes 0	Octets 2744
  Output Queue[1]: 0            (inet.0, inet-unicast)
```

Confirm the routes being sent over BGP:

```
jemurray@juniper-1> show route advertising-protocol bgp 192.168.99.1 logical-system LogicalSystem-1

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 10.100.0.0/16           Self                                    I
* 10.101.0.0/16           Self                                    I
* 10.102.0.0/16           Self                                    I
* 10.103.0.0/16           Self                                    I
* 10.104.0.0/16           Self                                    I
* 10.105.0.0/16           Self                                    I
```

Confirm the routes received over BGP:

```
jemurray@juniper-1> show route receive-protocol bgp 192.168.99.1 logical-system LogicalSystem-1

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 10.200.0.0/16           192.168.99.1                            64513 I
* 10.201.0.0/16           192.168.99.1                            64513 I
* 10.202.0.0/16           192.168.99.1                            64513 I
* 10.203.0.0/16           192.168.99.1                            64513 I
* 10.204.0.0/16           192.168.99.1                            64513 I
* 10.205.0.0/16           192.168.99.1                            64513 I

inet6.0: 1 destinations, 1 routes (1 active, 0 holddown, 0 hidden)
```

Confirm routes are installed into the routing table:

```
jemurray@juniper-1> show route logical-system LogicalSystem-1

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.100.0.0/16      *[Static/5] 01:02:17
                       Discard
10.101.0.0/16      *[Static/5] 01:02:17
                       Discard
10.102.0.0/16      *[Static/5] 01:02:17
                       Discard
10.103.0.0/16      *[Static/5] 01:02:17
                       Discard
10.104.0.0/16      *[Static/5] 01:02:17
                       Discard
10.105.0.0/16      *[Static/5] 01:02:17
                       Discard
10.200.0.0/16      *[BGP/170] 00:30:36, localpref 100
                      AS path: 64513 I, validation-state: unverified
                    >  to 192.168.99.1 via lt-0/0/0.101
10.201.0.0/16      *[BGP/170] 00:30:36, localpref 100
                      AS path: 64513 I, validation-state: unverified
                    >  to 192.168.99.1 via lt-0/0/0.101
10.202.0.0/16      *[BGP/170] 00:30:36, localpref 100
                      AS path: 64513 I, validation-state: unverified
                    >  to 192.168.99.1 via lt-0/0/0.101
10.203.0.0/16      *[BGP/170] 00:30:36, localpref 100
                      AS path: 64513 I, validation-state: unverified
                    >  to 192.168.99.1 via lt-0/0/0.101
10.204.0.0/16      *[BGP/170] 00:30:36, localpref 100
                      AS path: 64513 I, validation-state: unverified
                    >  to 192.168.99.1 via lt-0/0/0.101
10.205.0.0/16      *[BGP/170] 00:30:36, localpref 100
                      AS path: 64513 I, validation-state: unverified
                    >  to 192.168.99.1 via lt-0/0/0.101
192.168.99.0/31    *[Direct/0] 01:20:12
                    >  via lt-0/0/0.101
192.168.99.0/32    *[Local/0] 01:20:12
                       Local via lt-0/0/0.101

inet6.0: 1 destinations, 1 routes (1 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

ff02::2/128        *[INET6/0] 1d 16:16:37
                       MultiRecv
```