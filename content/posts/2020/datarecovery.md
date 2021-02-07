---
title: "Twenty years of history from old drives in the basement"
date: 2020-11-24T11:05:10-06:00
toc: false
images:
categories:
  - tech
tags: 
  - backups
  - datarecovery
  - ddrescue
  - waybackmachine
  - website
---

A [few days ago](/posts/2020/websitereconstruction/) I was looking into using the [WayBack Machine](https://archive.org/web/) to retrieve content from my old websites in order to consolidate posts in one location.  However, the WayBack machine was missing content and I had lost track of backups that may contain these old files.

I suspected this pile of drives in the basement may have the information I was looking for:

![](/images/2020-11-24-11-09-37.png)

However, I don't have hardware with SCSI, IDE, and SATA interfaces.  Google and Amazon to the rescue, 2 days later this arrived:

![](/images/2020-11-24-11-11-26.png)


After plugging the drives in, `lsblk` displayed the partitions:

```
jemurray@kali:~/old_drive_data/drive3-sun$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 238.5G  0 disk
├─sda1   8:1    0 237.5G  0 part /
├─sda2   8:2    0     1K  0 part
└─sda5   8:5    0   976M  0 part [SWAP]
sdb      8:16   0   8.5G  0 disk
├─sdb1   8:17   0   4.6G  0 part
├─sdb2   8:18   0 512.4M  0 part
├─sdb3   8:19   0   8.5G  0 part
└─sdb8   8:24   0   3.4G  0 part
```

I was shocked to find these drives were in working order.  In fact, only one had issues retrieving data.  For this drive, I used the GNU `ddrescue` utility and let it run over night.  It clinked, clattered, and made noises you would think had no chance of data recovery.   Six hours later, 99.99% of the sectors were recovered:

```
jemurray@kali:~/old_drive_data/drive3-sun$ sudo ddrescue /dev/sdb1 ./sdb1.img sdb1mapfile
[sudo] password for jemurray:
GNU ddrescue 1.23
Press Ctrl-C to interrupt
Initial status (read from mapfile)
rescued: 11730 kB, tried: 0 B, bad-sector: 0 B, bad areas: 0

Current status
     ipos:    4290 MB, non-trimmed:        0 B,  current rate:       0 B/s
     opos:    4290 MB, non-scraped:        0 B,  average rate:    227 kB/s
non-tried:        0 B,  bad-sector:    45056 B,    error rate:       2 B/s
  rescued:    4916 MB,   bad areas:       11,        run time:  5h 59m 38s
pct rescued:   99.99%, read errors:      101,  remaining time:         n/a
                              time since last successful read:  3h 24m 56s
Finished
```

This image is a UFS filesystem from my old Sun Ultra 10 workstation:

```
jemurray@kali:~/old_drive_data/drive3-sun$ file sdb1.img
sdb1.img: Unix Fast File system [v1] (big-endian), last mounted on /, last written at Thu Feb 23 13:33:55 2006, clean flag 253, number of blocks 4801608, number of data blocks 4724854, number of cylinder groups 109, block size 8192, fragment size 1024, minimum percentage of free blocks 1, rotational delay 0ms, disk rotational speed 90rps, TIME optimization
```


After `ddrescue` finished, the image(s) mounted without any trouble:  

```
jemurray@kali:~/old_drive_data/drive3-sun$ sudo mount ./sdb1.img /mnt
[sudo] password for jemurray:
mount: /mnt: WARNING: device write-protected, mounted read-only.
```

I can't recommend Linux enough.  It is able to mount NTFS, FAT, UFS, APFS, and of course EXT file systems.  No additional drivers or software was necessary.

The files are all there:

```
jemurray@kali:~/old_drive_data/drive3-sun$ ls -al /mnt
total 50
drwxr-xr-x 26 root root  512 Feb 17  2006 .
drwxr-xr-x 18 root root 4096 Nov 24 04:33 ..
lrwxrwxrwx  1 root root    9 Nov 26  2005 bin -> ./usr/bin
drwxr-xr-x  2 root root  512 Nov 30  2005 cdrom
drwxr-xr-x 17 root sys  4096 Feb 17  2006 dev
drwxr-xr-x  7 root sys   512 Nov 29  2005 devices
drwxr-xr-x 78 root sys  5120 Feb 17  2006 etc
drwxr-xr-x  3 root sys   512 Jan 29  2006 export
dr-xr-xr-x  2 root root  512 Nov 26  2005 home
```

Most importantly, I found the website files I was looking for.  

I believe this drive was part of an external backup system when I was using Linux as a desktop and hosting a webserver at XO:

```
jemurray@kali:~/old_drive_data/drive1/hk2/home/home/jemurray/public_html$ ls -al
total 22516
drwxr-xr-x 18 jemurray users        4096 Dec 31  2005 .
drwx-----x 22 jemurray users        4096 Feb 10  2006 ..
-rw-r--r--  1 root     root      3669129 Oct 13  2005 access_log
drwxr-xr-x  2 jemurray users        4096 Jan  9  2002 backup
drwxr-xr-x  2 jemurray users        4096 Jan 23  2005 bryce_mail
drwxr-xr-x  6 jemurray users        4096 Feb 18  2004 cellpix.org
drwx-----x  4 jemurray users        4096 Jan  7  2002 dev
-rwxr-xr-x  1 jemurray users       47017 May 10  2005 dnstop
-rw-r--r--  1 root     root       100524 Oct  8  2005 error_log
drwx------ 19 jemurray users        4096 Apr  6  2004 gallery
drwxr-xr-x  2 jemurray users        4096 May 31  2003 geoff
-rw-r--r--  1 jemurray users          20 Sep 30  2004 index.html
-r-xr-xr-x  1 jemurray users       28672 Apr  8  2005 Keygen.exe
drwxr-xr-x  4 jemurray users        4096 Jan  6  2002 laptux
drwxr-xr-x  2 jemurray jemurray     4096 Dec 31  2005 logs
drwxr-xr-x  4 jemurray users        4096 Oct  6  2004 ManSec
drwxr-xr-x  3 jemurray jemurray     4096 Oct  4  2005 mdev
drwxr-xr-x  4 jemurray users        4096 Dec 16  2003 mus
drwxr-xr-x 21 jemurray users        4096 Jul 17  2003 OLDSITE
drwxr-xr-x  2 jemurray users        4096 Dec 13  2003 resume
drwxr-xr-x  3 jemurray jemurray     4096 Dec 31  2005 visualeyes
drwxr-sr-x 25 jemurray jemurray     4096 Feb  5  2006 zweck
drwxr-xr-x 13 jemurray users        4096 Jul 17  2003 zweck-old
```

In addition to multiple versions of my website, 20 years of old email, IRC logs, and many other snippets of history that I believed were long gone were all there.  I feel like an archeologist on a historical dig.

The next step is to start reconstructing my old website articles and transferring them into markdown so Hugo can process them.  [Pandoc](/posts/2020/pandoc/) to the rescue here.
