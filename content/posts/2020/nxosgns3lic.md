---
title: "VIRL NX-OS licensing disabled"
date: 2020-06-23T12:27:36-05:00
toc: false
images:
categories:
  - tech
tags: 
  - nxos
  - gns3
  - licensing
  - virl
---

The NX-OS images that come with VIRL, which I am running in GNS3, do not have licensing enabled.   Attempting to enable a feature will result in the following error:

```
switch(config)# feature bgp
Feature grace period is disabled
```

To enable licensing run: 

```
switch(config)# license grace-period
switch(config)#
```

Now enable the feature:

```
switch(config)# feature bgp
LAN_ENTERPRISE_SERVICES_PKG license not installed. bgp feature will be shutdown
after grace period of approximately 120 day(s)
switch(config)# 2020 Jun 22 22:28:45 switch %LICMGR-2-LOG_LIC_NO_LIC: No license(s) present for feature LAN_ENTERPRISE_SERVICES_PKG. Application(s) shut down in 119 days.
2020 Jun 22 22:28:45 switch %LICMGR-2-LOG_LICAPP_NO_LIC: Application bgp running without LAN_ENTERPRISE_SERVICES_PKG license, shutdown in 119 days
```


