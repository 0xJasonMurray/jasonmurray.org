---
title: "Configuring Linux to respond to any IP address within a subnet block (example 10.0.0.0/8)"
date: 2021-09-24T16:12:08-05:00
toc: false
images:
categories:
  - tech
tags: 
  - linux
  - networking
  - anyip
description: Respond on any IP address using anyip in Linux
---

# TL;DR

Instead of manually configuring individual interfaces with IP addresses, enable `anyip` to respond to all ip addresses in the `10.0.0.0/8` subnet with the following command:

```text
sudo ip -4 route add local 10.0.0.0/8 dev lo
```

Confirm with `ping`:

```
# ping 10.123.12.45
PING 10.123.12.45 (10.123.12.45) 56(84) bytes of data.
64 bytes from 10.123.12.45: icmp_seq=1 ttl=64 time=0.050 ms
64 bytes from 10.123.12.45: icmp_seq=2 ttl=64 time=0.062 ms
64 bytes from 10.123.12.45: icmp_seq=3 ttl=64 time=0.062 ms
```

# Details

## Overview

For a variety of reasons, it may be necessary for a server to respond to any IP address within a given subnet.  For example, a honeypot attached to a /24 darknet subnet or a network lab simulator host which responds to anything in the 0.0.0.0/0 supernet.

Prior to [`anyip`](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ab79ad14a2d51e95f0ac3cef7cd116a57089ba82) in the Linux kernel, simulating multiple hosts required manually configuring IP addresses on a interface or sub-interfaces. In other words, one has to assign 16,777,216 static address configurations to simulate all the hosts within 10.0.0.0/8 CIDR block.

Let's explore using `anyip` to simulate 1 million hosts using a single static route.

On this Linux server, there are two interfaces `lo0` and `eth0`. `lo0` is configured with the typical `127.0.0.1` IP address and `eth0` is DHCP assigned `192.168.86.51`:

```
jemurray@kali-home [~]$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:24:ed:49 brd ff:ff:ff:ff:ff:ff
    inet 192.168.86.51/24 brd 192.168.86.255 scope global dynamic noprefixroute eth0
       valid_lft 85429sec preferred_lft 85429sec
```

There are no reachable addresses in the `10.0.0.0/8` networking space:

```
jemurray@kali-home [~]$ ping 10.1.1.1
PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
^C
--- 10.1.1.1 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2055ms
```
## Configuration: anyip on a single server

To enable `anyip` create a static route to the loopback interface using the subnet CIDR block the Linux server should respond to:

```text
sudo ip -4 route add local 10.0.0.0/8 dev lo
```

The output looks like this:

```
jemurray@kali-home [~]$ sudo ip -4 route add local 10.0.0.0/8 dev lo
```

Validate the route exists with `ip route` using the special `table 255`:

```
jemurray@kali-home [~]$  ip route show table 255
local 10.0.0.0/8 dev lo scope host 
```

Validate a specific route exists:

```text
sudo ip route get 10.0.34.2
```

The output should look similar to this:

```
jemurray@kali-home [~]$ sudo ip route get 10.0.34.2
local 10.0.34.2 dev lo src 10.0.34.2 uid 0
    cache <local>
```

Ping any random address in the `10.0.0.0/8` network and it will respond:

```
jemurray@kali-home [~]$ ping 10.1.1.1
PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
64 bytes from 10.1.1.1: icmp_seq=1 ttl=64 time=0.081 ms
64 bytes from 10.1.1.1: icmp_seq=2 ttl=64 time=0.066 ms
64 bytes from 10.1.1.1: icmp_seq=3 ttl=64 time=0.066 ms
^C
--- 10.1.1.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2045ms
rtt min/avg/max/mdev = 0.066/0.071/0.081/0.007 ms

jemurray@kali-home [~]$ ping 10.123.12.45
PING 10.123.12.45 (10.123.12.45) 56(84) bytes of data.
64 bytes from 10.123.12.45: icmp_seq=1 ttl=64 time=0.050 ms
64 bytes from 10.123.12.45: icmp_seq=2 ttl=64 time=0.062 ms
64 bytes from 10.123.12.45: icmp_seq=3 ttl=64 time=0.062 ms
^C
--- 10.123.12.45 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2053ms
rtt min/avg/max/mdev = 0.050/0.058/0.062/0.005 ms
```

## Configuration: anyip between 2 servers directly connected in the same subnet

What if we want to expand this out of a single machine?  In this example, I configured two Linux server on the same subnet like this:

[home-server] <-> [switch] <-> [kali-home]

Building on the example above where `kali-home` is configured with the `anyip` route.  We first test to see if the `home-server` can ping addresses in the `10.0.0.0/8`?  It can't because there is no route to it yet:

```
jemurray@home-server:~$ ping 10.123.123.1
PING 10.123.123.1 (10.123.123.1) 56(84) bytes of data.
^C
--- 10.123.123.1 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1009ms
```

On the `home-server` add a static route to `10.0.0.0/8` pointing to the IP address of the `kali-home` server:

```
jemurray@home-server:~$ sudo route add -net 10.0.0.0/8 gw 192.168.86.51
```

"Hosts" configured on a remote Linux server with `anyip` are now pingable:

```
jemurray@home-server:~$ ping 10.123.123.1
PING 10.123.123.1 (10.123.123.1) 56(84) bytes of data.
64 bytes from 10.123.123.1: icmp_seq=1 ttl=64 time=0.760 ms
64 bytes from 10.123.123.1: icmp_seq=2 ttl=64 time=0.528 ms
64 bytes from 10.123.123.1: icmp_seq=3 ttl=64 time=0.566 ms
^C
--- 10.123.123.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2038ms
rtt min/avg/max/mdev = 0.528/0.618/0.760/0.101 ms
jemurray@home-server:~$ ping 10.123.123.43
PING 10.123.123.43 (10.123.123.43) 56(84) bytes of data.
64 bytes from 10.123.123.43: icmp_seq=1 ttl=64 time=0.340 ms
64 bytes from 10.123.123.43: icmp_seq=2 ttl=64 time=0.520 ms
64 bytes from 10.123.123.43: icmp_seq=3 ttl=64 time=0.532 ms
64 bytes from 10.123.123.43: icmp_seq=4 ttl=64 time=0.522 ms
64 bytes from 10.123.123.43: icmp_seq=5 ttl=64 time=0.618 ms
^C
--- 10.123.123.43 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4078ms
rtt min/avg/max/mdev = 0.340/0.506/0.618/0.093 ms
```

Using `tcpdumpd`, we confirm this traffic is traversing the external `eth0` network interface:

```
jemurray@kali-home [~]$ sudo tcpdump -i eth0 icmp
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:19:55.500737 IP home-server.lan > 10.123.123.43: ICMP echo request, id 10130, seq 1, length 64
14:19:55.500783 IP 10.123.123.43 > home-server.lan: ICMP echo reply, id 10130, seq 1, length 64
14:19:56.507027 IP home-server.lan > 10.123.123.43: ICMP echo request, id 10130, seq 2, length 64
14:19:56.507067 IP 10.123.123.43 > home-server.lan: ICMP echo reply, id 10130, seq 2, length 64
14:19:57.531004 IP home-server.lan > 10.123.123.43: ICMP echo request, id 10130, seq 3, length 64
14:19:57.531048 IP 10.123.123.43 > home-server.lan: ICMP echo reply, id 10130, seq 3, length 64
14:19:58.554938 IP home-server.lan > 10.123.123.43: ICMP echo request, id 10130, seq 4, length 64
14:19:58.554979 IP 10.123.123.43 > home-server.lan: ICMP echo reply, id 10130, seq 4, length 64
14:19:59.579015 IP home-server.lan > 10.123.123.43: ICMP echo request, id 10130, seq 5, length 64
14:19:59.579069 IP 10.123.123.43 > home-server.lan: ICMP echo reply, id 10130, seq 5, length 64
```

# Why?

Many times over my career I needed to simulate a large number of hosts in a lab environment. Every time I would write a shell script to auto configure the interfaces and setup complex routing to tie it all together. Now, with a few simples static routes this complexity goes away.