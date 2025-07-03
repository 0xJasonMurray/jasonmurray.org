---
title: "Enable scp copies TO a Cisco device"
date: 2020-10-25T08:02:23-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - scp
---

# Overview

The `scp` server is disabled by default on Cisco switches and routers.  Enable `scp` to push files to the device.

# Details

Error:

```
jemurray@mbp-2019:~/Downloads $ scp -c aes128-cbc c3560c405-universalk9-mz.152-2.E10.bin 192.168.86.42:c3560c405-universalk9-mz.152-2.E10.bin
Password:
Administratively disabled.
jemurray@mbp-2019:~/Downloads $ client_loop: send disconnect: Broken pipe
```

Fix:

```
ip scp server enable
```

Try again:

```
jemurray@mbp-2019:~/Downloads $ scp -c aes128-cbc c3560c405-universalk9-mz.152-2.E10.bin 192.168.86.42:c3560c405-universalk9-mz.152-2.E10.bin
Password:
c3560c405-universalk9-mz.152-2.E10.bin     3%  624KB  58.0KB/s   05:23 ETA 
```

