---
title: "Erasing JunOS configuration"
date: 2020-10-08T10:36:05-05:00
toc: false
images:
categories:
  - tech
tags: 
  - juniper
  - erase
  - reset
---

To factory clear or erase the entire configuration on a JunOS device, use the `request system zeroize` command:

```
root@srx-lab> request system zeroize
warning: System will be rebooted and may not boot without configuration
Erase all data, including configuration and log files? [yes,no] (no) yes

warning: zeroizing re0

root@srx-lab> Waiting (max 60 seconds) for system process `vnlru' to stop...done
Waiting (max 60 seconds) for system process `vnlru_mem' to stop...done
Waiting (max 60 seconds) for system process `bufdaemon' to stop...done
Waiting (max 60 seconds) for system process `syncer' to stop...
Syncing disks, vnodes remaining...0 0 0 done

syncing disks... All buffers synced.
Uptime: 8m11s
Rebooting...
```
