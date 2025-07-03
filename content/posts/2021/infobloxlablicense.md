---
title: "How to generate and use temporary licenses on an Infoblox appliance"
date: 2021-04-18T06:54:04-05:00
toc: false
images:
categories:
  - tech
tags: 
  - infoblox
  - licenses
description: How to generate and use temporary licenses on an Infoblox appliance.
---

## Overview

For lab use, Infoblox offers 60 day temporary licenses. When the the trial period is over, use the `reset all licenses` command to reset the timer for another 60 days. But wait, it can't be that easy? There is a downside to resetting the licenses, all settings and configurations are wiped out at the same time. Rebuilding a production system every 60 days is not very practical, but for a lab it is OK.

Create a backup before the licenses expire, then restore afterwards.

## Details

After 60 days the licenses expire:

```
Infoblox > show license
Version		: 8.5.1-397728
Hardware ID	: 564DC844DC3F51B9BFA5103C3F768090

License Type	: Grid
Expiration Date	: Expired
License String	: GgAAAGqW/xQ0YFg0oY1fYzVqpxM1XudKArs4Qvm1

License Type	: DNS
Expiration Date	: Expired
License String	: EwAAAGuW+A13KVZs65QRYj4m6kktWuc=

License Type	: DHCP
Expiration Date	: Expired
License String	: FAAAAGuQ6AE6IRMh49FfYDwkplZ8CuBM

License Type	: DNS Traffic Control
Expiration Date	: Expired
License String	: EwAAAGuM6A13KVZs65QRYj4m6kl+W+c=
```

Run the `reset all licenses` command. As noted above, this wipes out ALL settings:

```
Infoblox > reset all licenses

The entire system will be erased to default settings and
all licenses will be removed.


WARNING: THIS WILL ERASE ALL DATA AND LOG FILES THAT HAVE
BEEN CREATED ON THIS SYSTEM.

ARE YOU SURE YOU WANT TO PROCEED? (y or n): y

RESETTING THE SYSTEM
```

After the systems reboots, run `set network` from the serial console to reconfigure the network setting to access the web GUI.

At the same time, run `set remote_console` to enable `ssh`.

Confirm the licenses are removed:

```
Infoblox > show license
Version		: 8.5.1-397728
Hardware ID	: 564DC844DC3F51B9BFA5103C3F768090
```

Not sure if it a problem with my session or not, but I can not set a temporary license over an `ssh` session to the appliance. The system will not let me answer `y` to the question:

```
Infoblox > set temp_license

  1. DNSone (DNS, DHCP)
  2. DNSone with Grid (DNS, DHCP, Grid)
  3. Network Services for Voice (DHCP, Grid)
  4. Add NIOS License
  5. Add DNS Server license
  6. Add DHCP Server license
  7. Add Grid license
  8. Add Microsoft management license
  9. Add Multi-Grid Management license
 1.  Add Query Redirection license
 2.  Add Response Policy Zones license
 3.  Add FireEye license
 4.  Add DNS Traffic Control license
 5.  Add Cloud Network Automation license
 6.  Add Security Ecosystem license
 7.  Add Threat Analytics license
 8.  Add Flex Grid Activation license
 9.  Add Flex Grid Activation for Managed Services license

Select license (1-18) or q to quit: 2

This action will generate a temporary 60-day DNSone with Grid license.
Are you sure you want to do this? (y or n):

Temporary license is not installed.
```

It does work from the serial console. I suspect this is another way to discourage the use of temporary licenses:

[![Image of infoblox serial console license](/images/2021-04-16-15-21-43.png)](/images/2021-04-16-15-21-43.png)

Validate the new licenses are installed:

```
Infoblox > show license
Version		: 8.5.1-397728
Hardware ID	: 564DC844DC3F51B9BFA5103C3F768090

License Type	: DNS
Expiration Date	: 06/15/2021
License String	: EwAAAGuW+A13KFZs55QRYj4n6kktWuM=

License Type	: DHCP
Expiration Date	: 06/15/2021
License String	: FAAAAGuQ6AE6IRIh491fYDwkp1Z/X+BM

License Type	: Grid
Expiration Date	: 06/15/2021
License String	: GgAAAGqW/xQ0YFg0oY1fYzRqpx81XudKA7s4Tvm1
```

After the network is setup and the licenses are installed, run through a basic setup in the web GUI. Once complete restore from backups:

[![Image of infoblox configuration screen](/images/2021-04-16-15-25-28.png)](/images/2021-04-16-15-25-28.png)

60 days later the fun begins again:

[![Image of ](/images/2021-04-16-15-38-01.png)](/images/2021-04-16-15-38-01.png)