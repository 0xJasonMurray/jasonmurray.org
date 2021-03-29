---
title: "Partitioning and formatting a disk in Linux with gdisk and ext4"
date: 2021-03-27T06:34:33-05:00
toc: false
images:
categories:
  - tech
tags: 
  - linux
  - gdisk
description: Partitioning and formatting a disk in Linux with gdisk and ext4
---

In this server, `/dev/sdb` is an unconfigured 36T disk in a RAID-5 configuration:

```
[jemurray@wuit-s-00229 ~]$ ls -al /dev/sd*
brw-rw----. 1 root disk 8,  0 Mar 24 10:08 /dev/sda
brw-rw----. 1 root disk 8,  1 Mar 24 10:08 /dev/sda1
brw-rw----. 1 root disk 8,  2 Mar 24 10:08 /dev/sda2
brw-rw----. 1 root disk 8,  3 Mar 24 10:08 /dev/sda3
brw-rw----. 1 root disk 8,  4 Mar 24 10:08 /dev/sda4
brw-rw----. 1 root disk 8, 16 Mar 24 10:08 /dev/sdb
```

Partition `/dev/sdb` with `gdisk`:

```
[jemurray@wuit-s-00229 ~]$ sudo gdisk /dev/sdb
GPT fdisk (gdisk) version 1.0.3

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.
```

Examine the disk with the `p` (print) option:

```
Command (? for help): p
Disk /dev/sdb: 78134640640 sectors, 36.4 TiB
Model: PERC H740P Mini
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): 94A7CF47-5E92-4201-909C-96A4DD15E1C8
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 78134640606
Partitions will be aligned on 2048-sector boundaries
Total free space is 78134640573 sectors (36.4 TiB)
```

Create a new partition with the `n` (new) option.  Keep the default options to use all available space:

```
Command (? for help): n
Partition number (1-128, default 1): 1
First sector (34-78134640606, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-78134640606, default = 78134640606) or {+-}size{KMGTP}:
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300):
Changed type of partition to 'Linux filesystem'
```

View and validate the newly created partition:

```
Command (? for help): p
Disk /dev/sdb: 78134640640 sectors, 36.4 TiB
Model: PERC H740P Mini
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): 94A7CF47-5E92-4201-909C-96A4DD15E1C8
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 78134640606
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048     78134640606   36.4 TiB    8300  Linux filesystem
```

Write the partition table to disk with the `w` (write) option:

```
Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): y
OK; writing new GUID partition table (GPT) to /dev/sdb.
The operation has completed successfully.
```

Partition `1` is created on `/dev/sdb`:

```
[jemurray@wuit-s-00229 ~]$ ls -al /dev/sd*
brw-rw----. 1 root disk 8,  0 Mar 24 10:08 /dev/sda
brw-rw----. 1 root disk 8,  1 Mar 24 10:08 /dev/sda1
brw-rw----. 1 root disk 8,  2 Mar 24 10:08 /dev/sda2
brw-rw----. 1 root disk 8,  3 Mar 24 10:08 /dev/sda3
brw-rw----. 1 root disk 8,  4 Mar 24 10:08 /dev/sda4
brw-rw----. 1 root disk 8, 16 Mar 26 15:31 /dev/sdb
brw-rw----. 1 root disk 8, 17 Mar 26 15:31 /dev/sdb1
```

Create an `ext4` partition on `/dev/sdb1` with `mkfs.ext4`:

```
[jemurray@wuit-s-00229 ~]$ sudo mkfs.ext4 /dev/sdb1
[sudo] password for jemurray:
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 9766829819 4k blocks and 610426880 inodes
Filesystem UUID: 5318a944-1274-4298-b33b-bb0efc7192fe
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
	102400000, 214990848, 512000000, 550731776, 644972544, 1934917632,
	2560000000, 3855122432, 5804752896

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done
```

Create a mount point directory for the new disk:

```
[jemurray@wuit-s-00229 ~]$ sudo mkdir /backup
```

Mount the disk:

```
[jemurray@wuit-s-00229 ~]$ sudo mount /dev/sdb1 /backup/
```

Validate the disk is mounted and its size:

```
[jemurray@wuit-s-00229 ~]$ df -h /backup/
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb1        37T   24K   35T   1% /backup
```

Mount the disk on reboot, by adding the following line to `/etc/fstab`:

```
[jemurray@wuit-s-00229 ~]$ grep backup /etc/fstab
/dev/sdb1 /backup ext4 defaults 1 2
```

Once the `/etc/fstab` has the proper device and mount point, attempt to mount the disk with directory name only:

```
[jemurray@wuit-s-00229 ~]$ sudo mount /backup
```

One final check to make sure the mount is working as expected:

```
[jemurray@wuit-s-00229 ~]$ sudo df -h /backup/
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb1        37T   24K   35T   1% /backup
```

