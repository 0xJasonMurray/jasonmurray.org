---
title: "How to log into the FirePower FTD CLI"
date: 2020-06-26T17:11:00-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - firepower
  - cli
---

Logging into a Cisco FirePower FTD CLI is not an obvious operation.

First log into FXOS chassis manager:

```
jemurray@mbp-2019:~ $ ssh firepower.example.com
Password:
Last login: Fri Jun 26 16:52:28 CDT 2020 from bastion.example.com on pts/0

Copyright 2004-2018, Cisco and/or its affiliates. All rights reserved.
Cisco is a registered trademark of Cisco Systems, Inc.
All other trademarks are property of their respective owners.

Cisco Fire Linux OS v6.2.3 (build 13)
Cisco Firepower 9000 Series SM-36 Threat Defense v6.2.3.12 (build 80)

Cisco Firepower Extensible Operating System (FX-OS) Software.
 TAC support: http://www.cisco.com/tac
 Copyright (c) 2009-2016, Cisco Systems, Inc. All rights reserved.
 The copyrights to certain works contained in this software are owned by other third parties and used and distributed under license.
 Certain components of this software are licensed under the 'GNU General Public License, version 3'
 provided with ABSOLUTELY NO WARRANTY under the terms of 'GNU General Public License, Version 3', available here:
 http://www.gnu.org/licenses/gpl.html. See User Manual (''Licensing'') for details.
 Certain components of this software are licensed under the 'GNU General Public License, version 2'
 provided with ABSOLUTELY NO WARRANTY under the terms of 'GNU General Public License, version 2', available here:
 http://www.gnu.org/licenses/old-licenses/gpl-2.0.html. See User Manual (''Licensing'') for details.
 Certain components of this software are licensed under the 'GNU LESSER GENERAL PUBLIC LICENSE, version 3'
 provided with ABSOLUTELY NO WARRANTY under the terms of 'GNU LESSER GENERAL PUBLIC LICENSE' Version 3, available here:
 http://www.gnu.org/licenses/lgpl.html. See User Manual (''Licensing'') for details.
 Certain components of this software are licensed under the 'GNU Lesser General Public License, version 2.1'
 provided with ABSOLUTELY NO WARRANTY under the terms of 'GNU Lesser General Public License, version 2', available here:
 http://www.gnu.org/licenses/old-licenses/lgpl-2.1.html. See User Manual (''Licensing'') for details.
 Certain components of this software are licensed under the 'GNU Library General Public License, version 2'
 provided with ABSOLUTELY NO WARRANTY under the terms of 'GNU Library General Public License, version 2', available here:
 http://www.gnu.org/licenses/old-licenses/lgpl-2.0.html. See User Manual (''Licensing'') for details.


	    Cisco Security Services Platform
		  Type ? for list of commands
Firepower-module1>
```

Connect to the FTD.  This is the firewall module of the FirePower:

```
Firepower-module1>connect ftd
Connecting to ftd() console... enter exit to return to bootCLI
>
>
```

**STOP!**  There is no need to go any further, you are in the FTD firewall management interface.  For those feeling adventurous or need to troubleshoot an advance issue, read on...

There is a underlying Linux OS that powers the FTD, it can be accessed by enabling expert mode.  This can be dangerous, changes made in this mode can damage the firewall and cause outages difficult to resolve.

```
> expert
admin@firepower:/opt/cisco/csp/applications$
```