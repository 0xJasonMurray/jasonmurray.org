---
title: "Using PerfSonar, pscheduler, and iperf3 to test network throughput problems"
date: 2020-08-17T14:24:22-05:00
toc: false
images:
categories:
  - tech
tags: 
  - iperf3
  - networking
  - asymmetric
  - troubleshooting
---

The PerfSonar project enables network engineers to perform a variety of troubleshooting tests.  While investigating a file transfer issue over Internet2, I was able to use `pscheduler` on the PerfSonar servers to test both upload and download throughput.

When a PerfSonar system is configured and online, it registers in the [Global PerfSonar Directory](http://stats.es.net/ServicesDirectory/).  Most of these servers are publicly available for realtime troubleshooting.

I was able to use the `pScheduler` utility to execute an `iperf3` throughput test on a es.net server.  The first run tested download speeds (no issue):

```
[jemurray@wuit-s-00050 ~]$ pscheduler task --tool iperf3 throughput -d bost-pt1.es.net -t 10 --reverse
Submitting task...
Task URL:
https://localhost/pscheduler/tasks/f350732b-7318-4c35-9360-79ce423c5655
Running with tool 'iperf3'
Fetching first run...

Next scheduled run:
https://localhost/pscheduler/tasks/f350732b-7318-4c35-9360-79ce423c5655/runs/1775f5f4-2a98-4ad1-81b2-cf1a71e08ffc
Starts 2020-08-17T17:31:45Z (~9 seconds)
Ends   2020-08-17T17:32:04Z (~18 seconds)
Waiting for result...

* Stream ID 5
Interval       Throughput     Retransmits    Current Window
0.0 - 1.0      6.33 Gbps      9              134.22 MBytes
1.0 - 2.0      8.35 Gbps      0              134.22 MBytes
2.0 - 3.0      8.47 Gbps      0              134.22 MBytes
3.0 - 4.0      8.39 Gbps      0              134.22 MBytes
4.0 - 5.0      8.26 Gbps      0              134.22 MBytes
5.0 - 6.0      8.38 Gbps      0              134.22 MBytes
6.0 - 7.0      8.35 Gbps      0              134.22 MBytes
7.0 - 8.0      8.34 Gbps      0              134.22 MBytes
8.0 - 9.0      8.39 Gbps      0              134.22 MBytes
9.0 - 10.0     8.44 Gbps      0              134.22 MBytes
10.0 - 10.0    8.59 Gbps      0              134.22 MBytes

Summary
Interval       Throughput     Retransmits
0.0 - 10.0     8.17 Gbps      9

No further runs scheduled.
```


The second run tested upload speeds.  Found the problem, 486Kbp/s - far from the 10Gb/s capacity it should normally perform at:

```
[jemurray@wuit-s-00050 ~]$ pscheduler task --tool iperf3 throughput -d bost-pt1.es.net -t 10
Submitting task...
Task URL:
https://localhost/pscheduler/tasks/71329e14-c5cd-489b-9600-0826be4457d1
Running with tool 'iperf3'
Fetching first run...

Next scheduled run:
https://localhost/pscheduler/tasks/71329e14-c5cd-489b-9600-0826be4457d1/runs/07b7cee2-4128-4970-ad04-a875b6a85d16
Starts 2020-08-17T17:30:35Z (~6 seconds)
Ends   2020-08-17T17:30:54Z (~18 seconds)
Waiting for result...

* Stream ID 5
Interval       Throughput     Retransmits    Current Window
0.0 - 1.0      3.87 Mbps      8              26.84 KBytes
1.0 - 2.0      0.00bps        2              8.95 KBytes
2.0 - 3.0      0.00bps        1              8.95 KBytes
3.0 - 4.0      0.00bps        0              8.95 KBytes
4.0 - 5.0      0.00bps        2              8.95 KBytes
5.0 - 6.0      0.00bps        8              8.95 KBytes
6.0 - 7.0      1.00 Mbps      5              8.95 KBytes
7.0 - 8.0      0.00bps        3              8.95 KBytes
8.0 - 9.0      0.00bps        2              26.84 KBytes
9.0 - 10.0     0.00bps        3              8.95 KBytes

Summary
Interval       Throughput     Retransmits
0.0 - 10.0     486.72 Kbps    34

No further runs scheduled.
```

Finally, `mtr` was run to map out the exact path:

```
[jemurray@wuit-s-00050 ~]$ mtr --report --report-wide -c 120 bost-pt1.es.net
Start: Mon Aug 17 12:49:52 2020
HOST: wuit-s-00050.accounts.ad.wustl.edu         Loss%   Snt   Last   Avg  Best  Wrst StDev
  1.|-- xe-0-0-8-900w-mmr-wu-rt-0.net.wustl.edu     0.0%   120    0.4   0.5   0.2   6.3   0.8
  2.|-- xe-0-0-1.944.rtr2.chic.indiana.gigapop.net  1.7%   120    5.6   5.6   5.5   6.5   0.0
  3.|-- 149.165.254.122                             0.8%   120    5.7   5.7   5.6   6.2   0.0
  4.|-- eqxchicr5-ip-b-chiccr5.es.net               2.5%   120    6.1   6.1   6.0   6.6   0.0
  5.|-- eqxashcr5-ip-a-eqxchicr5.es.net             4.2%   120   20.6  20.6  20.5  21.3   0.0
  6.|-- washcr5-ip-c-eqxashcr5.es.net               7.5%   120   21.1  21.0  20.9  21.7   0.0
  7.|-- aofacr5-ip-b-washcr5.es.net                84.2%   120  7973. 7954. 7871. 8013.  41.8
  8.|-- newycr5-ip-a-aofacr5.es.net                 9.2%   120   26.2  26.2  26.0  29.0   0.3
  9.|-- bostcr5-ip-a-newycr5.es.net                 2.5%   120   30.7  30.7  30.6  31.2   0.0
 10.|-- bost-pt1.es.net                             3.3%   120   30.4  30.4  30.3  30.5   0.0
```

By using these publicly available tools and the support person on the phone, the problem was quickly narrowed down to a receive optic on the far side of our service providers edge router.
