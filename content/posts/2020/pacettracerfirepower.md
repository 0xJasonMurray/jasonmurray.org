---
title: "Running packet-tracer on a Cisco FirePower firewall"
date: 2020-08-28T14:29:25-05:00
toc: false
images:
categories:
  - tech
tags: 
  - firepower
  - cisco
  - packet-tracer
---

Login into FXOS and connect to module 1 console:

```
jemurray@mbp-2019:~ $ firepower.example.com
Password:
 
Copyright 2004-2018, Cisco and/or its affiliates. All rights reserved.
Cisco is a registered trademark of Cisco Systems, Inc.
All other trademarks are property of their respective owners.
 
Cisco Fire Linux OS v6.2.3 (build 13)
Cisco Firepower 9000 Series SM-36 Threat Defense v6.2.3.12 (build 80)
 
Cisco Firepower Extensible Operating System (FX-OS) Software.
 TAC support: http://www.cisco.com/tac
 Copyright (c) 2009-2016, Cisco Systems, Inc. All rights reserved.
 
        Cisco Security Services Platform
          Type ? for list of commands
fw-0-A#> connect module 1 console
```

Connect to the FTD:

```
Firepower-module1>connect ftd
Connecting to ftd() console... enter exit to return to bootCLI
>
```

Connect to the diagnostic-cli:

```
> system support diagnostic-cli
Attaching to Diagnostic CLI ... Press 'Ctrl+a then d' to detach.
Type help or '?' for a list of available commands.
 
firepower> 
```

Enter enable mode:

```
firepower> en
firepower> enable
Password:
firepower#
```

Run the packet-tracer command:

```
packet-tracer input INSIDE tcp 192.168.0.1 65000 0050.5687.f3bd 192.168.1.1 22
```

Final output:

```
...lots of details...

Result:
input-interface: INPUT
input-status: up
input-line-status: up
output-interface: OUTPUT
output-status: up
output-line-status: up
Action: allow
```

The FMC has a packet-tracer GUI.  However, if the interface names are too long there is no way to select the right one without a lot of guessing:

![fmc interface](/images/fmcptinterface.png)