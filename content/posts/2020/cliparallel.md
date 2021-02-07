---
title: "Using the parallel command to speed up log file processing"
date: 2020-06-07T10:07:53-05:00
toc: false
images:
categories:
  - tech
tags: 
  - parallel
  - cli
  - awk
  - performance
---

# Summary

Processing large log files from the command line using tools such as `cat`, `grep`, and `awk` can be time consuming.  By using the command [`parallel`](https://www.gnu.org/software/parallel/), processing time can be reduced significantly.

In this example, the time it took to search through 24 large log files went from 130 seconds to 26 second by using the [`parallel`](https://www.gnu.org/software/parallel/) command:


```
[jemurraylinux-host 2020-06-01]$ time ls -1 smb_files.*.gz | parallel -P5 "zcat {} | grep scipy | wc" | awk '{sum+=$1} END {print sum}'
1557855

real	0m26.019s
```

# Details

Example input data is 24 hours of SMB access logs:

```
[jemurraylinux-host 2020-06-01]$ ls -al smb_files.*.gz
-rw-r--r--. 1 root root 61153434 Jun  1 01:03 smb_files.00:00:00-01:00:00.log.gz
-rw-r--r--. 1 root root 73379126 Jun  1 02:03 smb_files.01:00:00-02:00:00.log.gz
-rw-r--r--. 1 root root 68187575 Jun  1 03:03 smb_files.02:00:00-03:00:00.log.gz
-rw-r--r--. 1 root root 64462449 Jun  1 04:03 smb_files.03:00:00-04:00:00.log.gz
-rw-r--r--. 1 root root 52274261 Jun  1 05:03 smb_files.04:00:00-05:00:00.log.gz
-rw-r--r--. 1 root root 49936464 Jun  1 06:03 smb_files.05:00:00-06:00:00.log.gz
-rw-r--r--. 1 root root 52163412 Jun  1 07:03 smb_files.06:00:00-07:00:00.log.gz
-rw-r--r--. 1 root root 62623665 Jun  1 08:03 smb_files.07:00:00-08:00:00.log.gz
-rw-r--r--. 1 root root 69905009 Jun  1 09:03 smb_files.08:00:00-09:00:00.log.gz
-rw-r--r--. 1 root root 80785420 Jun  1 10:04 smb_files.09:00:00-10:00:00.log.gz
-rw-r--r--. 1 root root 88264516 Jun  1 11:03 smb_files.10:00:00-11:00:00.log.gz
-rw-r--r--. 1 root root 84752054 Jun  1 12:03 smb_files.11:00:00-12:00:00.log.gz
-rw-r--r--. 1 root root 81681862 Jun  1 13:03 smb_files.12:00:00-13:00:00.log.gz
-rw-r--r--. 1 root root 78398116 Jun  1 14:03 smb_files.13:00:00-14:00:00.log.gz
-rw-r--r--. 1 root root 84952968 Jun  1 15:03 smb_files.14:00:00-15:00:00.log.gz
-rw-r--r--. 1 root root 89052806 Jun  1 16:04 smb_files.15:00:00-16:00:00.log.gz
-rw-r--r--. 1 root root 81828336 Jun  1 17:03 smb_files.16:00:00-17:00:00.log.gz
-rw-r--r--. 1 root root 69743081 Jun  1 18:03 smb_files.17:00:00-18:00:00.log.gz
-rw-r--r--. 1 root root 58067071 Jun  1 19:04 smb_files.18:00:00-19:00:00.log.gz
-rw-r--r--. 1 root root 50089323 Jun  1 20:03 smb_files.19:00:00-20:00:00.log.gz
-rw-r--r--. 1 root root 50349351 Jun  1 21:03 smb_files.20:00:00-21:00:00.log.gz
-rw-r--r--. 1 root root 48452189 Jun  1 22:03 smb_files.21:00:00-22:00:00.log.gz
-rw-r--r--. 1 root root 50196889 Jun  1 23:03 smb_files.22:00:00-23:00:00.log.gz
-rw-r--r--. 1 root root 49307426 Jun  2 00:03 smb_files.23:00:00-00:00:00.log.gz
```

These log files contain approximately 1.6GB of data:

```
[jemurraylinux-host 2020-06-01]$ ls -al smb_files.*.gz | awk '{sum+=$5} END {print sum}'
1600006803
```

Processing each file in serial takes about 2 minutes and 10 seconds:

```
[jemurraylinux-host 2020-06-01]$ time zcat smb_files.*.gz | grep scipy | wc
1557855 24925680 546752855

real	2m10.014s
user	2m25.545s
sys	0m12.419s
```

Using the [`parallel`](https://www.gnu.org/software/parallel/) command reduces the processing time to 26 seconds:

```
[jemurraylinux-host 2020-06-01]$ time ls -1 smb_files.*.gz | parallel -P5 "zcat {} | grep scipy | wc" | awk '{sum+=$1} END {print sum}'
1557855

real	0m26.019s
user	2m17.196s
sys	0m12.157s
```

Be careful with the `-P` option on production systems.  It specifies the number jobslots to run.  0 means as many as possible. Default is 100% which will run one job per CPU core on each machine.  When processing a large number of files, it can easily fill up all available CPUs.


