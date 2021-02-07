---
title: "SSH: no matching cipher found"
date: 2020-10-23T05:55:47-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - ssh
  - cipher
---

On occasion we run across an old Cisco switch that throws the following error when connecting via `ssh` from a modern client:

```
jemurray@mbp-2019:~ $ ssh 192.168.86.42
Unable to negotiate with 192.168.86.42 port 22: no matching cipher found. Their offer: aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc
```

The best solution is to upgrade the software on the switch to something more modern.  If that is not possible, use the `-c` option with `ssh` to select one of the ciphers presented in the `Their offer:` section:

```
jemurray@mbp-2019:~ $ ssh -c aes128-cbc 192.168.86.42
Password:

3560sw-1#
```

