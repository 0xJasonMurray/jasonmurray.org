---
title: "Linux Traffic Control"
date: 2020-05-04T18:31:46-05:00
categories:
  - tech
tags: [til, tc, linux, networking]
---

Using the `tc` (traffic control) utility in Linux, it is possible to simulate various network conditions, such as latency, packet loss, random conditions, etc.

## List existing `tc` rules:

`sudo /usr/sbin/tc -p qdisc ls dev eth0`

## Delete existing `tc` rules:

`sudo /usr/sbin/tc -p qdisc del dev eth0 root`

## Add vs. Change

If a rule does not already exist, use the add command option:

`sudo /usr/sbin/tc qdisc add dev eth0 root netem delay 10ms`

If a rule already exists, use the change command option:

`sudo /usr/sbin/tc qdisc change dev eth0 root netem delay 10ms`

## Simple Delay / Latency

Add 10ms of delay / latency:

`sudo /usr/sbin/tc qdisc add dev eth0 root netem delay 10ms`

This command simply adds 10ms of delay:

```
jemurray@shell:~$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=57 time=1.57 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=57 time=1.37 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=57 time=1.29 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=57 time=1.29 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=57 time=1.25 ms
64 bytes from 8.8.8.8: icmp_seq=6 ttl=57 time=11.4 ms
64 bytes from 8.8.8.8: icmp_seq=7 ttl=57 time=11.4 ms
64 bytes from 8.8.8.8: icmp_seq=8 ttl=57 time=11.4 ms
64 bytes from 8.8.8.8: icmp_seq=9 ttl=57 time=11.3 ms
64 bytes from 8.8.8.8: icmp_seq=10 ttl=57 time=11.5 ms
64 bytes from 8.8.8.8: icmp_seq=11 ttl=57 time=11.4 ms
64 bytes from 8.8.8.8: icmp_seq=12 ttl=57 time=1.31 ms
64 bytes from 8.8.8.8: icmp_seq=13 ttl=57 time=1.43 ms
64 bytes from 8.8.8.8: icmp_seq=14 ttl=57 time=1.35 ms
64 bytes from 8.8.8.8: icmp_seq=15 ttl=57 time=1.32 ms
```

## Random Delay / Latency

`sudo /usr/sbin/tc qdisc add dev eth0 root netem delay 20ms 10ms 25%`

This command adds 20ms of delay, randomizes by +/- 10ms with a 25% variance:

```
jemurray@shell:~$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=57 time=1.57 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=57 time=1.35 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=57 time=1.35 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=57 time=1.29 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=57 time=1.42 ms
64 bytes from 8.8.8.8: icmp_seq=6 ttl=57 time=11.9 ms
64 bytes from 8.8.8.8: icmp_seq=7 ttl=57 time=17.2 ms
64 bytes from 8.8.8.8: icmp_seq=8 ttl=57 time=19.5 ms
64 bytes from 8.8.8.8: icmp_seq=9 ttl=57 time=27.4 ms
64 bytes from 8.8.8.8: icmp_seq=10 ttl=57 time=28.8 ms
64 bytes from 8.8.8.8: icmp_seq=11 ttl=57 time=30.1 ms
64 bytes from 8.8.8.8: icmp_seq=12 ttl=57 time=20.5 ms
64 bytes from 8.8.8.8: icmp_seq=13 ttl=57 time=19.10 ms
64 bytes from 8.8.8.8: icmp_seq=14 ttl=57 time=12.9 ms
64 bytes from 8.8.8.8: icmp_seq=15 ttl=57 time=14.7 ms
64 bytes from 8.8.8.8: icmp_seq=16 ttl=57 time=20.6 ms
64 bytes from 8.8.8.8: icmp_seq=17 ttl=57 time=16.7 ms
64 bytes from 8.8.8.8: icmp_seq=18 ttl=57 time=16.10 ms
64 bytes from 8.8.8.8: icmp_seq=19 ttl=57 time=22.9 ms
64 bytes from 8.8.8.8: icmp_seq=20 ttl=57 time=26.9 ms
64 bytes from 8.8.8.8: icmp_seq=21 ttl=57 time=25.8 ms
64 bytes from 8.8.8.8: icmp_seq=22 ttl=57 time=17.3 ms
64 bytes from 8.8.8.8: icmp_seq=23 ttl=57 time=22.7 ms
64 bytes from 8.8.8.8: icmp_seq=24 ttl=57 time=20.2 ms
64 bytes from 8.8.8.8: icmp_seq=25 ttl=57 time=29.7 ms
64 bytes from 8.8.8.8: icmp_seq=26 ttl=57 time=30.4 ms
64 bytes from 8.8.8.8: icmp_seq=27 ttl=57 time=13.5 ms
64 bytes from 8.8.8.8: icmp_seq=28 ttl=57 time=29.3 ms
64 bytes from 8.8.8.8: icmp_seq=29 ttl=57 time=26.4 ms
64 bytes from 8.8.8.8: icmp_seq=30 ttl=57 time=12.6 ms
64 bytes from 8.8.8.8: icmp_seq=31 ttl=57 time=1.29 ms
64 bytes from 8.8.8.8: icmp_seq=32 ttl=57 time=1.43 ms
64 bytes from 8.8.8.8: icmp_seq=33 ttl=57 time=1.30 ms
64 bytes from 8.8.8.8: icmp_seq=34 ttl=57 time=1.36 ms
```

## Random packet loss

`sudo /usr/sbin/tc qdisc change dev eth0 root netem loss 10% 25%`

This command simulates 10% packet loss with a 25% probability of successive loss:

```
                              My traceroute  [v0.92]
shell (104.131.191.87)                                            2020-05-04T23:52:38+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                 Packets               Pings
 Host                                            Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 104.131.0.254                                1.2%    82    0.4   1.4   0.4  20.2   3.0
 2. 138.197.248.6                                0.0%    82    8.8   1.2   0.5  23.0   2.9
 3. 138.197.244.44                               1.2%    82    1.0   1.3   0.9   5.7   0.8
 4. 162.243.191.243                              1.2%    82    3.3   1.4   1.0   8.5   1.1
 5. 108.170.248.97                               0.0%    82    1.5   1.4   1.3   1.7   0.1
 6. 172.253.72.129                               0.0%    81    1.5   1.5   1.4   1.8   0.1
 7. lga34s15-in-f14.1e100.net                    2.5%    81    1.4   1.5   1.4   1.6   0.1
```

## Using `tc` to manipulate performance testing with `iperf3`


Baseline test with no `tc` rules applied:

```
jemurray@shell:~$ iperf3 -t 10 -i 1 -c 128.252.5.113
Connecting to host 128.252.5.113, port 5201
[  5] local 104.131.191.87 port 43942 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  62.9 MBytes   527 Mbits/sec    3   5.49 MBytes
[  5]   1.00-2.00   sec  82.5 MBytes   692 Mbits/sec    0   5.49 MBytes
[  5]   2.00-3.00   sec  82.5 MBytes   692 Mbits/sec    0   5.49 MBytes
[  5]   3.00-4.00   sec  81.2 MBytes   682 Mbits/sec    0   5.49 MBytes
[  5]   4.00-5.00   sec  82.5 MBytes   692 Mbits/sec    0   5.49 MBytes
[  5]   5.00-6.00   sec  81.2 MBytes   682 Mbits/sec    0   5.49 MBytes
[  5]   6.00-7.00   sec  82.5 MBytes   692 Mbits/sec    0   5.49 MBytes
[  5]   7.00-8.00   sec  81.2 MBytes   682 Mbits/sec    0   5.49 MBytes
[  5]   8.00-9.00   sec  82.5 MBytes   692 Mbits/sec    0   5.49 MBytes
[  5]   9.00-10.00  sec  82.5 MBytes   692 Mbits/sec    0   5.49 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec   802 MBytes   672 Mbits/sec    3             sender
[  5]   0.00-10.04  sec   801 MBytes   669 Mbits/sec                  receiver

iperf Done.
```

`sudo /usr/sbin/tc qdisc add dev eth0 root netem loss 10% 25%`

Applying 10% packet loss:

```
jemurray@shell:~$ iperf3 -t 10 -i 1 -c 128.252.5.113
Connecting to host 128.252.5.113, port 5201
[  5] local 104.131.191.87 port 43946 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  1.28 MBytes  10.7 Mbits/sec    4   22.6 KBytes
[  5]   1.00-2.00   sec   764 KBytes  6.26 Mbits/sec    5   22.6 KBytes
[  5]   2.00-3.00   sec   764 KBytes  6.26 Mbits/sec    6   22.6 KBytes
[  5]   3.00-4.00   sec   382 KBytes  3.13 Mbits/sec    8   11.3 KBytes
[  5]   4.00-5.00   sec   382 KBytes  3.13 Mbits/sec    4   17.0 KBytes
[  5]   5.00-6.00   sec   382 KBytes  3.13 Mbits/sec   10   14.1 KBytes
[  5]   6.00-7.00   sec   382 KBytes  3.13 Mbits/sec    4   21.2 KBytes
[  5]   7.00-8.00   sec   764 KBytes  6.26 Mbits/sec    6   12.7 KBytes
[  5]   8.00-9.00   sec   382 KBytes  3.13 Mbits/sec    4   25.5 KBytes
[  5]   9.00-10.00  sec   382 KBytes  3.13 Mbits/sec    9   12.7 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  5.76 MBytes  4.83 Mbits/sec   60             sender
[  5]   0.00-10.04  sec  5.08 MBytes  4.25 Mbits/sec                  receiver

iperf Done.
```

`sudo /usr/sbin/tc qdisc change dev eth0 root netem delay 100ms 10ms 25%`

Adding 100ms of latency:

```
jemurray@shell:~$ iperf3 -t 10 -i 1 -c 128.252.5.113
Connecting to host 128.252.5.113, port 5201
[  5] local 104.131.191.87 port 43950 connected to 128.252.5.113 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  4.09 MBytes  34.3 Mbits/sec    4    543 KBytes
[  5]   1.00-2.00   sec  18.8 MBytes   157 Mbits/sec    0   4.35 MBytes
[  5]   2.00-3.00   sec  26.2 MBytes   220 Mbits/sec    0   4.35 MBytes
[  5]   3.00-4.00   sec  22.5 MBytes   189 Mbits/sec    0   4.35 MBytes
[  5]   4.00-5.00   sec  22.5 MBytes   189 Mbits/sec    0   4.35 MBytes
[  5]   5.00-6.00   sec  26.2 MBytes   220 Mbits/sec    0   4.35 MBytes
[  5]   6.00-7.00   sec  23.8 MBytes   199 Mbits/sec    0   4.35 MBytes
[  5]   7.00-8.00   sec  25.0 MBytes   210 Mbits/sec    0   4.35 MBytes
[  5]   8.00-9.00   sec  26.2 MBytes   220 Mbits/sec    0   4.35 MBytes
[  5]   9.00-10.00  sec  26.2 MBytes   220 Mbits/sec    0   4.35 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec   222 MBytes   186 Mbits/sec    4             sender
[  5]   0.00-10.13  sec   219 MBytes   182 Mbits/sec                  receiver

iperf Done.
```

There are a lot more examples [here](https://wiki.linuxfoundation.org/networking/netem) and [here](https://wiki.debian.org/TrafficControl).
