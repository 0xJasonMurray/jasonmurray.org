---
title: "Cisco aaa authorization exec needed to transfer files"
date: 2020-11-03T15:08:48-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - exec
  - scp
---

Error:

```
jemurray@mbp-2019:~/Downloads $ scp -c aes128-cbc c3560c405-universalk9-mz.152-2.E10.bin 192.168.86.42:
Password:
Privilege denied.
jemurray@mbp-2019:~/Downloads $ client_loop: send disconnect: Broken pipe
```

Fix:

```
aaa authorization exec default local
```

Try again:

```
jemurray@mbp-2019:~/Downloads $ scp -c aes128-cbc c3560c405-universalk9-mz.152-2.E10.bin 192.168.86.42:c3560c405-universalk9-mz.152-2.E10.bin
Password:
c3560c405-universalk9-mz.152-2.E10.bin     3%  624KB  58.0KB/s   05:23 ETA 
```
