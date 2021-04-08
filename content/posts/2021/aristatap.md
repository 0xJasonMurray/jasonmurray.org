---
title: "Basic Arista Tap Aggregation Configuration"
date: 2021-04-07T16:29:59-05:00
toc: false
images:
categories:
  - tech
tags: 
  - arista
  - tapagg
description: Basic Arista Tap Aggregation Configuration
---

## Overview

[Arista's](https://www.arista.com/) [Tap Aggregation](https://www.arista.com/en/solutions/technology-bulletins/585-tap-aggregation) feature turns  switch(es) into packet capture, aggregation, and distribution points. This setup allows network operators to setup capture points over large geographic areas then centralize the collection point to a single data center.

The following diagram is an example topology of two different geographic areas performing packet captures and sending the output to a centralized cluster of servers for analysis:

[![Image of arista topology](/images/arista-high-level-overview.png)](/images/arista-high-level-overview.png)



## Details

### Tap / Tool Mode

To enable Tap Aggregation mode, each port on the switch is configured as either a `tool` or a `tap`.  A `tool` port is an interface that replicates data streams received by one or more tap ports. A `tap` port is an interface that receives a packet capture from a physical tap or `span / monitor` port.


### Enable Tap Aggregation Mode

Enable `tap aggregation` mode in the global configuration:

```text
tap aggregation
   mode exclusive
```

### Example Tool Port Configuration

Configure ports facing the analysis cluster in `tool` mode. A `port-channel` may be used to increase throughput capacity by spreading the load to multiple servers in the analysis cluster:

```
interface Port-Channel1
   description server-aggregation-cluster
   load-interval 5
   switchport mode tool
   switchport tool group set TapAgg
!
interface Ethernet1
   description server-1
   load-interval 5
   channel-group 1 mode on
   switchport mode tool
   switchport tool group set TapAgg
!
interface Ethernet2
   description server-2
   load-interval 5
   channel-group 1 mode on
   switchport mode tool
   switchport tool group set TapAgg
!
interface Ethernet3
   description server-3
   load-interval 5
   channel-group 1 mode on
   switchport mode tool
   switchport tool group set TapAgg
```

In order to keep each network flow pinned to a single server in the port-channel, use the follow `load-balance` policy:

```
load-balance policies
   load-balance fm6000 profile symmetric_5_tuple
      no fields mac
      fields ip protocol dst-ip dst-port src-ip src-port
      distribution symmetric-hash mac-ip
```

## Capture Point Configuration

Configure ports facing routers or switches in `tag` mode. Routers send information to the Arista switches either through a physical inline tap or a `span / monitor` port:

```
interface Ethernet35
   description router-1
   speed forced 10000full
   switchport mode tap
   switchport tap default group TapAgg
!
interface Ethernet36
   description router-2
   speed forced 10000full
   switchport mode tap
   switchport tap default group TapAgg
!
interface Ethernet37
   description router-3
   speed forced 10000full
   switchport mode tap
   switchport tap default group TapAgg
```

## Switch to Switch Uplink Configuration

Configure uplink ports between switches in `tap` mode:

```
interface Ethernet47
   description aggregation-switch-1
   load-interval 5
   ingress load-balance profile symmetric_5_tuple
   switchport mode tap
   switchport tap default group TapAgg
!
interface Ethernet48
   description aggregation-switch-2
   load-interval 5
   speed forced 10000full
   ingress load-balance profile symmetric_5_tuple
   switchport mode tap
   switchport tap default group TapAgg
!
interface Ethernet49/1
   description aggregation-switch-3
   load-interval 5
   speed forced 40gfull
   ingress load-balance profile symmetric_5_tuple
   switchport mode tap
   switchport tap default group TapAgg
```