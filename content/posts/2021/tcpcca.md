---
title: "TCP Congestion Control Algorithms in Linux"
date: 2021-08-20T13:02:18-05:00
toc: false
images:
categories:
  - tech
tags: 
  - tcp
  - cca
  - cubic
  - reno
  - htcp
  - bbr
description: How to check and set TCP congestion control algorithms in Linux
---

# Overview

How to check and set TCP congestion control algorithms (CCA) in Linux.

# Details

## Check Current CAA

```text
sysctl net.ipv4.tcp_congestion_control
```

Output Example:
```
jemurray@home-server:~$ sysctl net.ipv4.tcp_congestion_control
net.ipv4.tcp_congestion_control = cubic
```

## View Available CCAs

Check for the default CCAs in Ubuntu:

```text
sysctl net.ipv4.tcp_available_congestion_control
```

Output Example:
```
jemurray@home-server:~$ sysctl net.ipv4.tcp_available_congestion_control
net.ipv4.tcp_available_congestion_control = reno cubic
```

## Load Additional Modules

Linux has quite a few different CCAs available: https://en.wikipedia.org/wiki/TCP_congestion_control - Not all are loaded in the kernel by default.  

List installed, but not necessarily loaded CCAs:

```text
ls -al /lib/modules/`uname -r`/kernel/net/ipv4/tcp*
```

Output Example:
```
jemurray@home-server:~$ ls -al /lib/modules/`uname -r`/kernel/net/ipv4/tcp*
-rw-r--r-- 1 root root 14113 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_bbr.ko
-rw-r--r-- 1 root root 10913 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_bic.ko
-rw-r--r-- 1 root root 13865 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_cdg.ko
-rw-r--r-- 1 root root 10041 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_dctcp.ko
-rw-r--r-- 1 root root  8401 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_diag.ko
-rw-r--r-- 1 root root  7489 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_highspeed.ko
-rw-r--r-- 1 root root  9985 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_htcp.ko
-rw-r--r-- 1 root root  8073 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_hybla.ko
-rw-r--r-- 1 root root  8985 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_illinois.ko
-rw-r--r-- 1 root root  8617 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_lp.ko
-rw-r--r-- 1 root root 12561 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_nv.ko
-rw-r--r-- 1 root root 13441 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_probe.ko
-rw-r--r-- 1 root root  6449 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_scalable.ko
-rw-r--r-- 1 root root 11073 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_vegas.ko
-rw-r--r-- 1 root root  7609 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_veno.ko
-rw-r--r-- 1 root root  8097 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_westwood.ko
-rw-r--r-- 1 root root  7945 May  7 19:44 /lib/modules/4.15.0-144-generic/kernel/net/ipv4/tcp_yeah.ko
```

Use `modprobe` to load additional modules:

```text
sudo /sbin/modprobe tcp_bbr
```

Output Example:
```
jemurray@home-server:~$ sudo /sbin/modprobe tcp_bbr

jemurray@home-server:~$ sysctl net.ipv4.tcp_available_congestion_control
net.ipv4.tcp_available_congestion_control = reno cubic bbr
```



## Temporarily Change CCA

Use `sysctl` to temporarily change the CCA to `bbr`:

```text
sudo /sbin/sysctl -w net.ipv4.tcp_congestion_control=bbr
```

```
jemurray@home-server:~$ sudo /sbin/sysctl -w net.ipv4.tcp_congestion_control=bbr
net.ipv4.tcp_congestion_control = bbr

jemurray@home-server:~$ sysctl net.ipv4.tcp_congestion_control
net.ipv4.tcp_congestion_control = bbr
```

## Permanently Change the CCA

```text
sudo su
echo "net.ipv4.tcp_congestion_control = bbr" >> /etc/sysctl.conf
sysctl -p
```

Output Example:

```
jemurray@home-server:~$ sudo su
root@home-server:/home/jemurray# echo "net.ipv4.tcp_congestion_control = bbr" >> /etc/sysctl.conf
root@home-server:/home/jemurray# sysctl -p
net.ipv4.tcp_congestion_control = bbr
```

## Testing with iPerf3

Test differences in CCA speeds using `-C` option to iPerf3:

```text
iperf3 -i 1 -t 10 -c iperf3server.example.com -C reno
```

Output Example:
```
jemurray@home-server:~$ iperf3 -i 1 -t 10 -c iperf3server.example.com -C reno
Connecting to host iperf3server.example.com, port 5201
[  4] local 192.168.86.5 port 43060 connected to iperf3server.example.com port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  65.0 MBytes   545 Mbits/sec  711   1.37 MBytes
[  4]   1.00-2.00   sec  83.8 MBytes   702 Mbits/sec    0   1.45 MBytes
[  4]   2.00-3.00   sec  85.4 MBytes   717 Mbits/sec    0   1.53 MBytes
[  4]   3.00-4.00   sec  90.8 MBytes   762 Mbits/sec    0   1.61 MBytes
[  4]   4.00-5.00   sec  93.8 MBytes   786 Mbits/sec    2   1.68 MBytes
[  4]   5.00-6.00   sec  88.1 MBytes   739 Mbits/sec    0   1.76 MBytes
[  4]   6.00-7.00   sec  92.0 MBytes   772 Mbits/sec    0   1.83 MBytes
[  4]   7.00-8.00   sec  91.2 MBytes   765 Mbits/sec    0   1.89 MBytes
[  4]   8.00-9.00   sec  88.7 MBytes   744 Mbits/sec    0   1.96 MBytes
[  4]   9.00-10.00  sec  90.5 MBytes   759 Mbits/sec    1   2.02 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   869 MBytes   729 Mbits/sec  714             sender
[  4]   0.00-10.00  sec   867 MBytes   727 Mbits/sec                  receiver

iperf Done.


jemurray@home-server:~$ iperf3 -i 1 -t 10 -c iperf3server.example.com -C bbr
Connecting to host iperf3server.example.com, port 5201
[  4] local 192.168.86.5 port 43064 connected to iperf3server.example.com port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  53.5 MBytes   448 Mbits/sec  3265   3.29 MBytes
[  4]   1.00-2.00   sec  80.2 MBytes   673 Mbits/sec  957   3.47 MBytes
[  4]   2.00-3.00   sec  85.4 MBytes   717 Mbits/sec    0   3.37 MBytes
[  4]   3.00-4.00   sec  87.5 MBytes   734 Mbits/sec    0   3.49 MBytes
[  4]   4.00-5.00   sec  85.6 MBytes   718 Mbits/sec    0   3.39 MBytes
[  4]   5.00-6.00   sec  86.5 MBytes   726 Mbits/sec    0   3.32 MBytes
[  4]   6.00-7.00   sec  85.4 MBytes   716 Mbits/sec    0   3.31 MBytes
[  4]   7.00-8.00   sec  88.7 MBytes   744 Mbits/sec    0   3.34 MBytes
[  4]   8.00-9.00   sec  90.9 MBytes   763 Mbits/sec    0   4.11 MBytes
[  4]   9.00-10.00  sec  89.9 MBytes   754 Mbits/sec    0   3.23 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   834 MBytes   699 Mbits/sec  4222             sender
[  4]   0.00-10.00  sec   832 MBytes   698 Mbits/sec                  receiver

iperf Done.
```