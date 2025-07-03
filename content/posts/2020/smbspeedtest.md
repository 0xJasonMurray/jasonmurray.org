---
title: "Testing network perfomance using SMB, dd, and bypassing local disks on Linux"
date: 2020-05-23T14:18:51-05:00
toc: false
images:
categories:
  - tech
tags: 
  - smb
  - dd
  - networking
  - hpc
  - 10g
---

# Summary
Bypass disk performance constraints by writing to /dev/null and reading from /dev/zero:

Write to /dev/null:

```
dd status=progress if=/path/to/largefile.img of=/dev/null bs=128M
```

Read from /dev/zero:

```
dd status=progress if=/dev/zero of=/path/to/largefile.img bs=100M count=1000
```



# Details

## Overview

Modern network infrastructure is no longer the congestion point when it comes to transferring data to and from a server. Disk I/O, which comprises the bus, controller, and disk speed, are typically the cause of poor performance.   

By using /dev/zero and /dev/null, we can bypass poor performing disks to test the full capabilities of the network.

In this example, the end-to-end path is a 9000 byte MTU 10G network on a mix of Juniper QFX and MX devices. The server is a Dell PowerEdge R220 with a single disk (no RAID) and IBM Spectrum Scale provides the remote SMB storage.


## Reading from remote SMB file storage and writing to /dev/null 

Writing to /dev/null averages 1.1GB/s, copying 321GB in under 5 minutes:

```
[jemurray@linux-host Active]$ time dd status=progress if=/mnt/fiotest of=/dev/null bs=128M
321451458560 bytes (321 GB) copied, 283.120319 s, 1.1 GB/s
2400+0 records in
2400+0 records out
322122547200 bytes (322 GB) copied, 283.698 s, 1.1 GB/s

real 4m43.717s
user 0m0.019s
sys 1m28.993s
```

![SMB Read Graph](/images/sssmbread.png)


## Reading from /dev/zero and writing to remote SMB file storage 

Reading from /dev/zero averages near 1GB/s, copying 105GB in under 2 minutes:

```
[jemurray@linux-host Archive]$ time sudo dd status=progress if=/dev/zero of=/mnt/jem-test.img bs=100M count=1000
104647884800 bytes (105 GB) copied, 109.345025 s, 957 MB/s
1000+0 records in
1000+0 records out
104857600000 bytes (105 GB) copied, 109.68 s, 956 MB/s

real 1m51.792s
user 0m0.027s
sys 1m18.834s
```

Even though the graph does not report the exact utilization, the raw interface stats do:

```
jemurray@juniper-qfx> show interfaces xe-0/0/1 statistics | match rate
  Input rate   : 1289144448 bps (18204 pps)
  Output rate   : 43082640 bps (3498 pps)
```

![SMB Write Graph](/images/sssmbwrite.png)



Validate the file exists and size: 

```
[jemurray@linux-host Archive]$ ls -alh jem-test.img
-rwxr-xr-x 1 root root 98G May 22 14:49 jem-test.img
```


## Reading from remote SMB file storage and writing to physical disks

Attempting to write to physical disk reduces the transfer speed from 1GB/s to 141MB/s, spikes the CPU, and puts the system into 50%+ I/O WAIT.

It took over 12 minutes to copy 100GB from remote SMB to physical disk:

```
[jemurray@linux-host ~]$ time sudo dd status=progress if=/mnt/jem-test.img of=/home/jemurray/jem-test.img bs=128M
[sudo] password for nssadmin:
104857600000 bytes (105 GB) copied, 744.892203 s, 141 MB/s
781+1 records in
781+1 records out
104857600000 bytes (105 GB) copied, 744.958 s, 141 MB/s

real 12m32.206s
user 0m0.049s
sys 1m6.266s
```

CPU load on the system increases:

```
top - 15:02:52 up 336 days, 21:39, 2 users, load average: 5.92, 2.99, 1.28
Tasks: 231 total, 2 running, 229 sleeping, 0 stopped, 0 zombie
%Cpu(s): 0.1 us, 4.7 sy, 0.0 ni, 28.6 id, 66.6 wa, 0.0 hi, 0.0 si, 0.0 st
KiB Mem : 7837972 total, 105472 free, 2656840 used, 5075660 buff/cache
KiB Swap: 8126460 total, 7690692 free, 435768 used. 4709296 avail Mem

  PID USER PR NI VIRT RES SHR S  %CPU %MEM TIME+ COMMAND
13909 root 20 0 239184 131780 600 R 11.3 1.7 0:20.46 dd
```


I/O wait is > 50%:

```
-bash-4.2$ vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r b   swpd free buff cache   si so   bi bo in   cs us sy id wa st
 2 5 435768 122700 1108 5051828 0 0 0 3 0 0 0 0 99 0 0
 0 5 435768 135144 1108 5041064 0 0 52 131116 2410 5085 0 5 38 57 0
 0 5 435768 144840 1108 5030760 0 0 60 147456 2300 4683 0 4 38 58 0
 0 5 435768 132800 1108 5043940 0 0 32 131080 2246 5018 0 4 27 68 0
 0 4 435768 143248 1108 5032504 0 0 64 131072 2134 4704 0 5 27 69 0
 0 5 435768 142960 1108 5031760 0 0 0 147464 2141 4433 0 4 13 82 0
 1 3 435768 132364 1108 5041872 0 0 44 131112 2911 5104 1 4 40 55 0
 0 4 435768 145640 1108 5027828 0 0 24 131087 2718 4760 0 4 47 49 0
```


Writes average 130MB/s:

```
-bash-4.2$ iostat -dm 1 3
Linux 3.10.0-957.21.3.el7.x86_64 05/22/2020 _x86_64_ (4 CPU)

Device:   tps MB_read/s MB_wrtn/s MB_read MB_wrtn
sda 1.45 0.00 0.01 3914 379529

Device:   tps MB_read/s MB_wrtn/s MB_read MB_wrtn
sda 259.00 0.03 127.51 0 127

Device:   tps MB_read/s MB_wrtn/s MB_read MB_wrtn
sda 281.00 0.00 140.50 0 140
```
