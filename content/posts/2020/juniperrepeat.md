---
title: "Using refresh on the Juniper CLI to repeat a command"
date: 2020-05-15T10:29:02-05:00
toc: false
images:
categories:
  - tech
tags:
  - juniper
  - cli
  - refresh
  - til
---

Juniper CLI commands can be piped to `| refresh` in order to repeat the same action at a specific interval:


```
jemurray@900w-mmr-wu-rt-0> show interfaces ge-1/1/0 extensive | match err | refresh 5
---(refreshed at 2020-05-15 10:27:04 CDT)---
  Link-level type: Ethernet, MTU: 1514, MRU: 1522, LAN-PHY mode, Speed: 1000mbps, BPDU Error: None, MAC-REWRITE Error: None, Loopback: Disabled, Source filtering: Disabled,
  Input errors:
    Errors: 0, Drops: 0, Framing errors: 0, Runts: 0, Policed discards: 0, L3 incompletes: 0, L2 channel errors: 0, L2 mismatch timeouts: 0, FIFO errors: 0,
    Resource errors: 0
  Output errors:
    Carrier transitions: 0, Errors: 0, Drops: 0, Collisions: 0, Aged packets: 0, FIFO errors: 0, HS link CRC errors: 0, MTU errors: 2960, Resource errors: 0
    CRC/Align errors                         0                0
    FIFO errors                              0                0
    Total errors                             0                0
    Output packet error count                                 0
      Addresses, Flags: Is-Preferred Is-Primary
---(refreshed at 2020-05-15 10:27:09 CDT)---
  Link-level type: Ethernet, MTU: 1514, MRU: 1522, LAN-PHY mode, Speed: 1000mbps, BPDU Error: None, MAC-REWRITE Error: None, Loopback: Disabled, Source filtering: Disabled,
  Input errors:
    Errors: 0, Drops: 0, Framing errors: 0, Runts: 0, Policed discards: 0, L3 incompletes: 0, L2 channel errors: 0, L2 mismatch timeouts: 0, FIFO errors: 0,
    Resource errors: 0
  Output errors:
    Carrier transitions: 0, Errors: 0, Drops: 0, Collisions: 0, Aged packets: 0, FIFO errors: 0, HS link CRC errors: 0, MTU errors: 2960, Resource errors: 0
    CRC/Align errors                         0                0
    FIFO errors                              0                0
    Total errors                             0                0
    Output packet error count                                 0
      Addresses, Flags: Is-Preferred Is-Primary
```
