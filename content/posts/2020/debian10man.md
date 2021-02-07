---
title: "No man on Debian 10"
date: 2020-06-19T13:37:41-05:00
toc: false
images:
categories:
  - tech
tags: 
  - man
  - debian
  - apt
---

The default install of Debian 10 does not have `man` installed by default.  Attempting to view a `man` page results in the following error:

```
jemurray@shell:/usr/bin$ man basename
-bash: man: command not found
```

In the 20+ years I have used Linux, I have never found a distribution that didn't have `man` installed, but appears so:

```
jemurray@shell:/usr/bin$ which man
jemurray@shell:/usr/bin$

jemurray@shell:/usr/bin$ /usr/bin/man
-bash: /usr/bin/man: No such file or directory

jemurray@shell:/usr/bin$ /bin/man
-bash: /bin/man: No such file or directory

jemurray@shell:/usr/bin$ whereis man
man: /usr/local/man /usr/share/man /usr/share/man/man7/man.7.gz
```

Install `man`:

```
jemurray@shell:~$ sudo apt install man-db

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  groff-base libpipeline1 libuchardet0
Suggested packages:
  groff
The following NEW packages will be installed:
  groff-base libpipeline1 libuchardet0 man-db
0 upgraded, 4 newly installed, 0 to remove and 1 not upgraded.
Need to get 2,286 kB of archives.
After this operation, 6,700 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://deb.debian.org/debian buster/main amd64 libuchardet0 amd64 0.0.6-3 [64.9 kB]
Get:2 http://deb.debian.org/debian buster/main amd64 groff-base amd64 1.22.4-3 [916 kB]
Get:3 http://deb.debian.org/debian buster/main amd64 libpipeline1 amd64 1.5.1-2 [31.2 kB]
Get:4 http://deb.debian.org/debian buster/main amd64 man-db amd64 2.8.5-2 [1,274 kB]
Fetched 2,286 kB in 0s (14.8 MB/s)
Preconfiguring packages ...
Selecting previously unselected package libuchardet0:amd64.
(Reading database ... 79513 files and directories currently installed.)
Preparing to unpack .../libuchardet0_0.0.6-3_amd64.deb ...
Unpacking libuchardet0:amd64 (0.0.6-3) ...
Selecting previously unselected package groff-base.
Preparing to unpack .../groff-base_1.22.4-3_amd64.deb ...
Unpacking groff-base (1.22.4-3) ...
Selecting previously unselected package libpipeline1:amd64.
Preparing to unpack .../libpipeline1_1.5.1-2_amd64.deb ...
Unpacking libpipeline1:amd64 (1.5.1-2) ...
Selecting previously unselected package man-db.
Preparing to unpack .../man-db_2.8.5-2_amd64.deb ...
Unpacking man-db (2.8.5-2) ...
Setting up libpipeline1:amd64 (1.5.1-2) ...
Setting up libuchardet0:amd64 (0.0.6-3) ...
Setting up groff-base (1.22.4-3) ...
Setting up man-db (2.8.5-2) ...
Building database of manual pages ...
Created symlink /etc/systemd/system/timers.target.wants/man-db.timer → /lib/systemd/system/man-db.timer.
man-db.service is a disabled or a static unit, not starting it.
Processing triggers for libc-bin (2.28-10) ...
Processing triggers for mime-support (3.62) ...
```

Validate `man` is installed:

```
jemurray@shell:~$ which man
/usr/bin/man
```

It works now:

```
jemurray@mbp-2019:~ $ man basename | head

BASENAME(1)               BSD General Commands Manual              BASENAME(1)

NAME
     basename, dirname -- return filename or directory portion of pathname
```

