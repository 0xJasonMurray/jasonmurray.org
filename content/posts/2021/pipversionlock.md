---
title: "Pipversionlock"
date: 2021-04-22T09:20:42-05:00
draft: true
toc: false
images:
categories:
  - nocat
tags: 
  - untagged
description:
---

```
[jemurray@wuit-s-00229 ~]$ sudo pip3 install rdiff-backup==1.9.2rc0
[sudo] password for jemurray:
WARNING: Running pip install with root privileges is generally not a good idea. Try `pip3 install --user` instead.
Collecting rdiff-backup==1.9.2rc0
  Downloading https://files.pythonhosted.org/packages/e5/1a/b0ef36958d5a82172d4acffe9e9af1e36b5e0d12dfad6e68d5516e83fe46/rdiff_backup-1.9.2rc0-cp36-cp36m-manylinux1_x86_64.whl (310kB)
    100% |████████████████████████████████| 317kB 4.9MB/s
Installing collected packages: rdiff-backup
Successfully installed rdiff-backup-1.9.2rc0
[jemurray@wuit-s-00229 ~]$ which rdiff-backup
/usr/local/bin/rdiff-backup
[jemurray@wuit-s-00229 ~]$ rdiff-backup --version
rdiff-backup 1.9.2rc0
```

