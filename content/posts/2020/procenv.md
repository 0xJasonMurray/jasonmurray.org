---
title: "Using the /proc file system to gather information about the current process"
date: 2020-12-17T16:42:04-06:00
toc: false
images:
categories:
  - tech
tags: 
  - proc
  - linux
---

# Overview

The `/proc` directory is a pseudo-file system. It doesn't contain 'real' files but runtime system information (e.g. system memory, devices mounted, hardware configuration, etc).  In order words, it contains information about the running server as reported by the kernel.

There is a vast amount of information in the `/proc` directory.  Today I needed to find information about the current process without running any commands.   By using the `/proc/self/*` files, this information is readily available.  


# Details

In this example, we are looking at the `/proc` file systems for the current running process, `self`:

```
jemurray@home-server:/proc/self$ ls -al
total 0
dr-xr-xr-x   9 jemurray jemurray 0 Dec 17 16:26 .
dr-xr-xr-x 238 root     root     0 Oct  1 18:14 ..
dr-xr-xr-x   2 jemurray jemurray 0 Dec 17 16:27 attr
-rw-r--r--   1 jemurray jemurray 0 Dec 17 16:27 autogroup
-r--------   1 jemurray jemurray 0 Dec 17 16:27 auxv
-r--r--r--   1 jemurray jemurray 0 Dec 17 16:27 cgroup
--w-------   1 jemurray jemurray 0 Dec 17 16:27 clear_refs
-r--r--r--   1 jemurray jemurray 0 Dec 17 16:26 cmdline
-rw-r--r--   1 jemurray jemurray 0 Dec 17 16:27 comm
-rw-r--r--   1 jemurray jemurray 0 Dec 17 16:27 coredump_filter
-r--r--r--   1 jemurray jemurray 0 Dec 17 16:27 cpuset
lrwxrwxrwx   1 jemurray jemurray 0 Dec 17 16:27 cwd -> /proc/32093
-r--------   1 jemurray jemurray 0 Dec 17 16:27 environ
lrwxrwxrwx   1 jemurray jemurray 0 Dec 17 16:27 exe -> /bin/bash
dr-x------   2 jemurray jemurray 0 Dec 17 16:26 fd
dr-x------   2 jemurray jemurray 0 Dec 17 16:27 fdinfo
-rw-r--r--   1 jemurray jemurray 0 Dec 17 16:27 gid_map
-r--------   1 jemurray jemurray 0 Dec 17 16:27 io
-r--r--r--   1 jemurray jemurray 0 Dec 17 16:27 limits
```

To find the `uid` of the current user, view the `/proc/self/loginuid`:

```
jemurray@home-server:/proc$ cat self/loginuid
1000
```

UserID is confirmed with the `id` command:

```
jemurray@home-server:/proc$ id
uid=1000(jemurray) gid=1000(jemurray) groups=1000(jemurray),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```

This is just one small example, explore all the files for more information.

