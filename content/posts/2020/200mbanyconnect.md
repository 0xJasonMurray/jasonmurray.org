---
title: "Cisco AnyConnect 4.x 200Mb/s max throughput"
date: 2020-09-29T15:37:31-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - asa
  - anyconnect
  - vpn
---

Last week I upgraded to the ATT 1G fiber service.  This gave me a chance to retest the Cisco AnyConnect client VPN beyond the 100Mb/s cable modem previously in use.

- AnyConnect client: 4.8.x and 4.9.02028
- Cisco ASA: 9.12(3)12

Cisco has another bug that limits the AnyConnect VPN client to ~200Mb/s:

```
jemurray@mbp-2019:~ $ iperf3 -c 128.252.5.113 -i 1 -t 10
Connecting to host 128.252.5.113, port 5201
[  5] local 10.227.227.25 port 61227 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  13.7 MBytes   115 Mbits/sec
[  5]   1.00-2.00   sec  16.4 MBytes   137 Mbits/sec
[  5]   2.00-3.00   sec  14.5 MBytes   122 Mbits/sec
[  5]   3.00-4.00   sec  21.8 MBytes   183 Mbits/sec
[  5]   4.00-5.00   sec  21.2 MBytes   178 Mbits/sec
[  5]   5.00-6.00   sec  20.8 MBytes   174 Mbits/sec
[  5]   6.00-7.00   sec  19.4 MBytes   163 Mbits/sec
[  5]   7.00-8.00   sec  21.7 MBytes   182 Mbits/sec
[  5]   8.00-9.00   sec  17.2 MBytes   144 Mbits/sec
[  5]   9.00-10.00  sec  19.2 MBytes   161 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.00  sec   186 MBytes   156 Mbits/sec                  sender
[  5]   0.00-10.02  sec   185 MBytes   155 Mbits/sec                  receiver


jemurray@mbp-2019:~ $ iperf3 -c 128.252.5.113 -i 1 -t 10 -R
Connecting to host 128.252.5.113, port 5201
Reverse mode, remote host 128.252.5.113 is sending
[  5] local 10.227.227.25 port 61248 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  23.0 MBytes   193 Mbits/sec
[  5]   1.00-2.00   sec  28.0 MBytes   235 Mbits/sec
[  5]   2.00-3.00   sec  27.9 MBytes   234 Mbits/sec
[  5]   3.00-4.00   sec  23.0 MBytes   193 Mbits/sec
[  5]   4.00-5.00   sec  25.6 MBytes   215 Mbits/sec
[  5]   5.00-6.00   sec  26.2 MBytes   220 Mbits/sec
[  5]   6.00-7.00   sec  25.9 MBytes   217 Mbits/sec
[  5]   7.00-8.00   sec  25.7 MBytes   216 Mbits/sec
[  5]   8.00-9.00   sec  25.3 MBytes   212 Mbits/sec
[  5]   9.00-10.00  sec  26.0 MBytes   218 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.02  sec   263 MBytes   220 Mbits/sec  131             sender
[  5]   0.00-10.00  sec   257 MBytes   215 Mbits/sec                  receiver
```

The bug report is documented here:

https://bst.cloudapps.cisco.com/bugsearch/bug/CSCvf32537/?rfs=iqvred

Upgrading to 4.9(86)+ has not resolved the issue.  Speed increased slightly, but nowhere near the 700Mb/s+ I can get without the VPN:

```
jemurray@empathy:~$ iperf3 -c 128.252.5.113
Connecting to host 128.252.5.113, port 5201
[  5] local 10.227.224.135 port 35446 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  23.4 MBytes   196 Mbits/sec   38    503 KBytes       
[  5]   1.00-2.00   sec  26.8 MBytes   225 Mbits/sec    0    547 KBytes       
[  5]   2.00-3.00   sec  30.3 MBytes   254 Mbits/sec    0    576 KBytes       
[  5]   3.00-4.00   sec  29.1 MBytes   244 Mbits/sec    0    609 KBytes       
[  5]   4.00-5.00   sec  32.6 MBytes   273 Mbits/sec    0    644 KBytes       
[  5]   5.00-6.00   sec  32.6 MBytes   273 Mbits/sec    0    681 KBytes       
[  5]   6.00-7.00   sec  34.9 MBytes   293 Mbits/sec    0    716 KBytes       
[  5]   7.00-8.00   sec  34.9 MBytes   293 Mbits/sec    0    750 KBytes       
[  5]   8.00-9.00   sec  32.6 MBytes   273 Mbits/sec   17    557 KBytes       
[  5]   9.00-10.00  sec  30.3 MBytes   254 Mbits/sec    0    634 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec   307 MBytes   258 Mbits/sec   55             sender
[  5]   0.00-10.02  sec   305 MBytes   256 Mbits/sec                  receiver

jemurray@empathy:~$ iperf3 -c 128.252.5.113 -R
Connecting to host 128.252.5.113, port 5201
Reverse mode, remote host 128.252.5.113 is sending
[  5] local 10.227.224.135 port 35452 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  21.6 MBytes   181 Mbits/sec                  
[  5]   1.00-2.00   sec  25.2 MBytes   211 Mbits/sec                  
[  5]   2.00-3.00   sec  27.8 MBytes   233 Mbits/sec                  
[  5]   3.00-4.00   sec  34.0 MBytes   285 Mbits/sec                  
[  5]   4.00-5.00   sec  25.1 MBytes   211 Mbits/sec                  
[  5]   5.00-6.00   sec  25.3 MBytes   212 Mbits/sec                  
[  5]   6.00-7.00   sec  29.2 MBytes   245 Mbits/sec                  
[  5]   7.00-8.00   sec  33.6 MBytes   282 Mbits/sec                  
[  5]   8.00-9.00   sec  23.1 MBytes   194 Mbits/sec                  
[  5]   9.00-10.00  sec  26.5 MBytes   222 Mbits/sec                  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.02  sec   276 MBytes   231 Mbits/sec  410             sender
[  5]   0.00-10.00  sec   271 MBytes   228 Mbits/sec                  receiver
```