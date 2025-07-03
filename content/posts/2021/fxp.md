---
title: "Juniper fxp0 management interface"
date: 2021-03-26T11:04:05-05:00
toc: false
images:
categories:
  - tech
tags: 
  - juniper
  - networking
description:
---

## Overview

On a Juniper router the `fxp0` interface does not show up in the "standard" `interface` configuration output. Instead, it is `grouped` with the `router engines` configuration.

## Details

Looking at the routing table, we see the `172.21.16.0/24` network is associated with the `fxp0.0` interface:

```
jemurray@LAB-MX480> show route 172.21.16.0

inet.0: 9 destinations, 10 routes (9 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.21.16.0/24     *[Direct/0] 6d 23:01:48
                    >  via fxp0.0
                    [Direct/0] 6d 23:01:48
                    >  via fxp0.0
```

`show interfaces` details information about the `fxp0.0` interface:

```
jemurray@LAB-MX480> show interfaces fxp0.0
  Logical interface fxp0.0 (Index 6) (SNMP ifIndex 13)
    Flags: Up SNMP-Traps 0x4004000 Encapsulation: ENET2
    Input packets : 577260
    Output packets: 47143
    Protocol inet, MTU: 1500
    Max nh cache: 75000, New hold nh limit: 75000, Curr nh cnt: 4, Curr new hold cnt: 0, NH drop cnt: 0
      Flags: Sendbcast-pkt-to-re, Is-Primary
      Addresses, Flags: Master-only Is-Default Is-Preferred Is-Primary
        Destination: 172.21.16/24, Local: 172.21.16.45, Broadcast: 172.21.16.255
      Addresses
        Destination: 172.21.16/24, Local: 172.21.16.46, Broadcast: 172.21.16.255
```

But where is the configuration?

```
jemurray@LAB-MX480> show configuration interfaces fxp0

jemurray@LAB-MX480>
```

The `fxp0` interfaces are configured under the `route engines`: 

```
jemurray@LAB-MX480> show configuration groups re0 interfaces fxp0
unit 0 {
    family inet {
        filter {
            input-list LAB_PROTECT_BOX;
        }
        address 172.21.16.46/24;
        address 172.21.16.45/24 {
            master-only;
        }
    }
}
```