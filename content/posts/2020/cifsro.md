---
title: "SMB: Cannot mount //path/folder read-only"
date: 2020-05-24T10:11:19-05:00
toc: false
images:
categories:
  - tech
tags: 
  - smb
  - read-only
  - cifs-utils
---

# Summary

Install the `cifs-utils` package on Linux if you receive this error `mount: cannot mount //path/folder read-only` when trying to mount a remote SMB file system.


# Details

While trying to mount a remote SMB directory on a CentOS 7.x server, this error happened:

```
[jemurray@linux-host ~]$ sudo mount -v -t cifs -o username=jemurray,vers=3.0  //192.168.16.8/jemurray /mnt
mount: //192.168.16.8/jemurray is write-protected, mounting read-only
mount: cannot mount //192.168.16.8/jemurray read-only
```

This was logged:

```
May 22 14:08:23 linux-host kernel: Status code returned 0xc000006d STATUS_LOGON_FAILURE
May 22 14:08:23 linux-host kernel: CIFS VFS: Send error in SessSetup = -13
May 22 14:08:23 linux-host kernel: CIFS VFS: cifs_mount failed w/return code = -13
```

To resolve the issue, the `cifs-utils` package needs to be installed:

```
[jemurray@linux-host ~]$ sudo yum install cifs-utils
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.gigenet.com
 * epel: mirror.sfo12.us.leaseweb.net
 * extras: centos.mirrors.hoobly.com
 * perfSONAR: mirror.net.cen.ct.gov
 * updates: mirrors.gigenet.com
Resolving Dependencies
--> Running transaction check
---> Package cifs-utils.x86_64 0:6.2-10.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===============================================================================================================================================================================================
 Package                                         Arch                                        Version                                           Repository                                 Size
===============================================================================================================================================================================================
Installing:
 cifs-utils                                      x86_64                                      6.2-10.el7                                        base                                       85 k

Transaction Summary
===============================================================================================================================================================================================
Install  1 Package

Total download size: 85 k
Installed size: 175 k
Is this ok [y/d/N]: y
Downloading packages:
cifs-utils-6.2-10.el7.x86_64.rpm                                                                                                                                        |  85 kB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : cifs-utils-6.2-10.el7.x86_64                                                                                                                                                1/1
  Verifying  : cifs-utils-6.2-10.el7.x86_64                                                                                                                                                1/1

Installed:
  cifs-utils.x86_64 0:6.2-10.el7

Complete!
```

Prior to installing the `cifs-utils` package the `mount.cifs` command was not available:

```
[jemurray@linux-host log]# stat /usr/sbin/mount.cifs
  File: ‘/usr/sbin/mount.cifs’
  Size: 41016     	Blocks: 88         IO Block: 4096   regular file
Device: fd00h/64768d	Inode: 33947173    Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2020-05-22 14:10:30.456201338 -0500
Modify: 2017-08-03 11:58:33.000000000 -0500
Change: 2020-05-22 14:10:24.825131521 -0500    <------- Install date
 Birth: -
```

Per the `man` page, mount.cifs is needed to pass specific arguments such as username and password for authenticated mount points:

> mount.cifs mounts a Linux CIFS filesystem. It is usually invoked indirectly by the mount(8) command when using the "-t cifs" option.

Running an `strace` confirms the system is looking for `mount.cifs`:

```
sudo strace mount -v -t cifs -o username=jemurray,vers=3.0  //192.168.16.8/jemurray /mnt
...
stat("/sbin/mount.cifs", 0x7ffe09793b80) = -1 ENOENT (No such file or directory)
stat("/sbin/fs.d/mount.cifs", 0x7ffe09793b80) = -1 ENOENT (No such file or directory)
stat("/sbin/fs/mount.cifs", 0x7ffe09793b80) = -1 ENOENT (No such file or directory)
```

Mounting the drive after installing `cifs-utils`, was successful:

```
[jemurray@linux-host ~]$ sudo mount -v -t cifs -o username=jemurray,vers=3.0  //192.168.16.8/jemurray /mnt
Password for jemurray@//192.168.16.8/jemurray:  ******************
mount.cifs kernel mount options: ip=192.168.16.8,unc=\\192.168.16.8\jemurray,vers=3.0,user=jemurray,pass=***************
```
