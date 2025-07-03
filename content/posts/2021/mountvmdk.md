---
title: "How to mount a vmdk file in Linux"
date: 2021-01-09T14:29:34-06:00
toc: false
images:
categories:
  - tech
tags: 
  - mount
  - vmdk
  - linux
---

# Overview

`vmdk`'s, the underlying filesystem commonly used in `vmware`, can be mounted on a Linux server using the `qemu` utilities.  In this example, I downloaded an `ova`, unarchived it, and retrieved the `vmdk` files.  Once exposed, I am able to mount the filesystems embedded within the `vmdk`.   

In this example, the `ova` is a virtual "appliance" which prohibits access a low level root shell.  By mounting the `vmdk` file system directly, we can take a peek at the hidden secrets the vendor keeps under lock and key.   

# Details

Install qemu utils:

```
sudo apt install qemu-utils
```

Load the network block device module:

```
jemurray@home-server:~/ib$ sudo modprobe nbd
```

Create the network block devices from the `vmdk` image:

```
jemurray@home-server:~/ib$ sudo qemu-nbd -r -c /dev/nbd1 ./vnios-disk.vmdk
```

Examine the images `qemu` created:

```
jemurray@home-server:~/ib$ ls -al /dev/nbd1p*
brw-rw---- 1 root disk 43, 33 Jan  9 14:28 /dev/nbd1p1
brw-rw---- 1 root disk 43, 34 Jan  9 14:28 /dev/nbd1p2
brw-rw---- 1 root disk 43, 35 Jan  9 14:28 /dev/nbd1p3
brw-rw---- 1 root disk 43, 36 Jan  9 14:28 /dev/nbd1p4
brw-rw---- 1 root disk 43, 37 Jan  9 14:28 /dev/nbd1p5
brw-rw---- 1 root disk 43, 38 Jan  9 14:28 /dev/nbd1p6
brw-rw---- 1 root disk 43, 39 Jan  9 14:28 /dev/nbd1p7
```

Create mount points for the new filesystems:

```
jemurray@home-server:~/ib$ mkdir p1
jemurray@home-server:~/ib$ mkdir p2
jemurray@home-server:~/ib$ mkdir p3
jemurray@home-server:~/ib$ mkdir p4
jemurray@home-server:~/ib$ mkdir p5
jemurray@home-server:~/ib$ mkdir p6
jemurray@home-server:~/ib$ mkdir p7
```

Attempt to mount each individual directory:

```
jemurray@home-server:~/ib$ sudo mount /dev/nbd1p1 ./p1
mount: /home/jemurray/ib/p1: WARNING: device write-protected, mounted read-only.

jemurray@home-server:~/ib$ sudo mount /dev/nbd1p2 ./p2
mount: /home/jemurray/ib/p2: WARNING: device write-protected, mounted read-only.

jemurray@home-server:~/ib$ sudo mount /dev/nbd1p3 ./p3
NTFS signature is missing.
Failed to mount '/dev/nbd1p3': Invalid argument
The device '/dev/nbd1p3' doesn't seem to have a valid NTFS.
Maybe the wrong device is used? Or the whole disk instead of a
partition (e.g. /dev/sda, not /dev/sda1)? Or the other way around?

jemurray@home-server:~/ib$ sudo mount /dev/nbd1p4 ./p4
mount: /home/jemurray/ib/p4: WARNING: device write-protected, mounted read-only.

jemurray@home-server:~/ib$ sudo mount /dev/nbd1p5 ./p5
mount: /home/jemurray/ib/p5: WARNING: device write-protected, mounted read-only.

jemurray@home-server:~/ib$ sudo mount /dev/nbd1p6 ./p6
mount: /home/jemurray/ib/p6: WARNING: device write-protected, mounted read-only.

jemurray@home-server:~/ib$ sudo mount /dev/nbd1p7 ./p7
mount: /home/jemurray/ib/p7: unknown filesystem type 'swap'.
```

Listing files:

```
jemurray@home-server:~/ib/p1$ ls -al
total 104
drwxr-xr-x 23 root     root      4096 Oct  8 12:42 .
drwxrwxr-x  9 jemurray jemurray  4096 Jan  9 14:31 ..
drwxr-xr-x  2 root     root      4096 Oct  8 12:41 bin
drwxr-xr-x  4 root     root      4096 Oct  8 12:42 boot
drwxr-xr-x  4 root     root      4096 Oct  8 12:41 dev
drwxr-xr-x 29 root     root      4096 Oct  8 12:42 etc
drwxr-xr-x  2 root     root      4096 Oct  8 12:41 home
drwxr-xr-x  7 root     root      4096 Oct  8 12:42 lib
drwx------  2 root     root     16384 Oct  8 12:58 lost+found
drwxr-xr-x  5 root     root      4096 Oct  8 12:41 media
drwxr-xr-x  2 root     root      4096 Oct  8 12:41 mnt
```