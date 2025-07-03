---
title: "Display progress of data moved through a unix pipe (|) with pv"
date: 2021-02-24T15:24:46-06:00
toc: false
images:
categories:
  - tech
tags: 
  - unix
  - pv
  - du
  - grep
  - pipe
description: Examine the movement of data through a unix pipe (|) with the pv command.  Results are displayed in time elapsed, total throughput, current Mb/s, etc.
---

Examine the movement of data through a unix pipe (|) with the `pv` command.  Results are displayed in time elapsed, total throughput, current Mb/s, etc.

In this example, we will take several GB of compressed log file data and process it with `grep`:

```text
[jemurray@linux-host 2021-02-23]$ du -ach logfile.*
2.1G	logfile.00:00:00-01:00:00.log.gz
1.9G	logfile.01:00:00-02:00:00.log.gz
1.9G	logfile.02:00:00-03:00:00.log.gz
1.9G	logfile.03:00:00-04:00:00.log.gz
1.8G	logfile.04:00:00-05:00:00.log.gz
1.9G	logfile.05:00:00-06:00:00.log.gz
2.1G	logfile.06:00:00-07:00:00.log.gz
2.7G	logfile.07:00:00-08:00:00.log.gz
3.1G	logfile.08:00:00-09:00:00.log.gz
3.2G	logfile.09:00:00-10:00:00.log.gz
3.3G	logfile.10:00:00-11:00:00.log.gz
3.3G	logfile.11:00:00-12:00:00.log.gz
3.4G	logfile.12:00:00-13:00:00.log.gz
3.3G	logfile.13:00:00-14:00:00.log.gz
3.2G	logfile.14:00:00-15:00:00.log.gz
3.1G	logfile.15:00:00-16:00:00.log.gz
3.0G	logfile.16:00:00-17:00:00.log.gz
2.6G	logfile.17:00:00-18:00:00.log.gz
2.5G	logfile.18:00:00-19:00:00.log.gz
2.3G	logfile.19:00:00-20:00:00.log.gz
2.3G	logfile.20:00:00-21:00:00.log.gz
2.3G	logfile.21:00:00-22:00:00.log.gz
2.1G	logfile.22:00:00-23:00:00.log.gz
2.0G	logfile.23:00:00-00:00:00.log.gz
61G	total
```

`pv` outputs the total bytes, current data rate, and time elapsed:

```text
[jemurray@linux-host 2021-02-23]$ zcat logfile.* | pv | grep exampletext
1.55GiB 0:00:16 [ 101MiB/s] [         <=>                                                                       ]
```

