---
title: "Capturing packets on Cisco ASA 5585-SSP40 drops per flow bandwidth to 1Gb/s"
date: 2021-10-20T19:59:45-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - asa
  - capture
  - networking
description: Running packet captures on a Cisco ASA significantly reduces available bandwidth. Don't forget to turn them off when finished.
---
TL;DR: Capturing packets on a Cisco ASA significantly reduces available bandwidth. Don't forget to turn them off when finished.

While performing a routine network performance test, I noticed a reduction in available bandwidth on a link normally able to carry around 2.5Gb/s.

Using `iperf3` between two 10Gb/s capable [perfsonar](https://perfsonar.net/) nodes with an ASA 5585 SSP40 in the middle, I was only able to push around 1Gb/s:

```
[user@perfsonar-2 ~]$ iperf3 -t 10 -i 1 -c perfsonar-1.example.com
Connecting to host perfsonar-1.example.com, port 5201
[  5] local 10.39.253.177 port 59110 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   127 MBytes  1.06 Gbits/sec  1226   1010 KBytes
[  5]   1.00-2.00   sec   120 MBytes  1.01 Gbits/sec    0   1.14 MBytes
[  5]   2.00-3.00   sec   121 MBytes  1.02 Gbits/sec    0   1.28 MBytes
[  5]   3.00-4.00   sec   120 MBytes  1.01 Gbits/sec    0   1.52 MBytes
[  5]   4.00-5.00   sec   120 MBytes  1.01 Gbits/sec   88    949 KBytes
[  5]   5.00-6.00   sec   120 MBytes  1.01 Gbits/sec    0   1.08 MBytes
[  5]   6.00-7.00   sec   120 MBytes  1.01 Gbits/sec    0   1.22 MBytes
[  5]   7.00-8.00   sec   119 MBytes   996 Mbits/sec    0   1.42 MBytes
[  5]   8.00-9.00   sec   120 MBytes  1.01 Gbits/sec    0   1.71 MBytes
[  5]   9.00-10.00  sec   120 MBytes  1.01 Gbits/sec   86   1.02 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  1.18 GBytes  1.01 Gbits/sec  1400             sender
[  5]   0.00-10.03  sec  1.17 GBytes  1.00 Gbits/sec                  receiver
```

Confirmed the ASA is running packet captures on the inside interface:

```
asa-1/pri/context-1/act# show capture
capture capture-1 type raw-data interface inside [Buffer Full - 524184 bytes]
  match ip host 192.168.0.1 host 192.168.0.2
capture capture-2 type raw-data interface inside [Capturing - 1128 bytes]
  match ip host 192.168.0.3 host 192.168.0.4
```

Disable the captures:

```
asa-1/context-1# no capture capture-1
asa-1/context-1# no capture capture-2
```

Confirm with `iperf3` the transfer rates increases from 1Gb/s to 2.61Gb/s:

```
[user@perfsonar-2 ~]$ iperf3 -t 10 -i 1 -c perfsonar-1.example.com
Connecting to host perfsonar-1.example.com, port 5201
[  5] local 10.39.253.177 port 59254 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   268 MBytes  2.25 Gbits/sec  875   1.91 MBytes
[  5]   1.00-2.00   sec   334 MBytes  2.80 Gbits/sec    0   2.12 MBytes
[  5]   2.00-3.00   sec   331 MBytes  2.78 Gbits/sec    8   1.36 MBytes
[  5]   3.00-4.00   sec   331 MBytes  2.78 Gbits/sec    0   1.64 MBytes
[  5]   4.00-5.00   sec   332 MBytes  2.79 Gbits/sec    0   1.89 MBytes
[  5]   5.00-6.00   sec   330 MBytes  2.77 Gbits/sec    0   2.31 MBytes
[  5]   6.00-7.00   sec   264 MBytes  2.21 Gbits/sec  427    418 KBytes
[  5]   7.00-8.00   sec   274 MBytes  2.30 Gbits/sec   82    707 KBytes
[  5]   8.00-9.00   sec   316 MBytes  2.65 Gbits/sec  132    727 KBytes
[  5]   9.00-10.00  sec   330 MBytes  2.77 Gbits/sec    0   1.15 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  3.04 GBytes  2.61 Gbits/sec  1524             sender
[  5]   0.00-10.04  sec  3.03 GBytes  2.59 Gbits/sec                  receiver
```
