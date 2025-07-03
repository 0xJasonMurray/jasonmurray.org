---
title: "Linux caches MTU path maximum size"
date: 2020-05-11T07:29:54-05:00
toc: false
images:
categories:
  - tech
tags: 
  - mtu
  - tcpdump
  - icmp
  - tshark
  - linux
  - kernel
---

While troubleshooting a MTU path discovery issue, `ping` returned this error message: `ping: local error: Message too long, mtu=1500` and `tcpdump` stopped logging ICMP packets egressing the interface.

Since this issue comes up on occasion and is not well understood, here is a brief explanation.


Examine the Linux MTU cache before MTU discovery:

```
[jemurray@wuit-s-00047 ~]$ sudo ip route get 104.131.191.87
104.131.191.87 via 128.252.5.114 dev p1p1 src 128.252.5.113
    cache
```

Sending a ping with MTU < 1500 bytes to validate the path is working as expected:

```
[jemurray@wuit-s-00047 ~]$ ping 104.131.191.87
PING 104.131.191.87 (104.131.191.87) 56(84) bytes of data.
64 bytes from 104.131.191.87: icmp_seq=1 ttl=47 time=36.0 ms
64 bytes from 104.131.191.87: icmp_seq=2 ttl=47 time=35.8 ms
^C
```

Confirm that Linux did not discover the max MTU path size, it is still within the default 1500 bytes:

```
[jemurray@wuit-s-00047 ~]$ sudo ip route get 104.131.191.87
104.131.191.87 via 128.252.5.114 dev p1p1 src 128.252.5.113
    cache
```

Sending a packet using ICMP > 1500 bytes, note the ICMP `Frag needed` (refer to the packet capture below for more details):

```
[jemurray@wuit-s-00047 ~]$ ping -M do -s 1500 shell.jasonmurray.org
PING jasonmurray.org (104.18.61.146) 1500(1528) bytes of data.
From xe-0-0-1-900w-mmr-wu-rt-0.net.wustl.edu (128.252.100.126) icmp_seq=1 Frag needed and DF set (mtu = 1500)
ping: local error: Message too long, mtu=1500
ping: local error: Message too long, mtu=1500
ping: local error: Message too long, mtu=1500
```

Linux caches the 1500 byte maximum MTU path length:

```
[jemurray@wuit-s-00047 ~]$ sudo ip route get 104.131.191.87
104.131.191.87 via 128.252.5.114 dev p1p1 src 128.252.5.113
    cache expires 579sec mtu 1500
```

All subsequent attempts to send packets larger than 1500 bytes are dropped by the kernel before they are placed on the wire:

```
ping: local error: Message too long, mtu=1500
ping: local error: Message too long, mtu=1500
ping: local error: Message too long, mtu=1500
```

This is confirmed with `tcpdump`.  There are no more packets leaving the interface after the routing table caches the max MTU path:

```
[jemurray@wuit-s-00047 ~]$ tcpdump -n -r ./icmp-mtu.pcap
reading from file ./icmp-mtu.pcap, link-type EN10MB (Ethernet)
17:29:11.357654 IP 128.252.5.113 > 104.18.61.146: ICMP echo request, id 29508, seq 1, length 1508
17:29:11.358762 IP 128.252.100.126 > 128.252.5.113: ICMP 104.18.61.146 unreachable - need to frag (mtu 1500), length 36
...no more icmp packets leave the interface after the ICMP unreachable tells the routing table cache what the max MTU size is...
```

In the example above, the internal campus network is capable of transmitting 9000 byte packets.  The MTU discovery does not happen until the packet attempts to egress the commodity Internet.  See the `tracepath` and `tcpdump` below for more details.

Use tracepath to document the full path to end host, refer to this path when looking at the packet decode below:

```
[jemurray@wuit-s-00047 ~]$ tracepath -n 104.131.191.87
 1?: [LOCALHOST]                                         pmtu 9000
 1:  128.252.5.114                                         0.974ms
 1:  128.252.5.114                                         1.055ms
 2:  128.252.1.42                                          1.339ms asymm  3
 3:  128.252.100.126                                       1.922ms asymm  4  <---- This router sent back the ICMP 'need to frag' message
 4:  128.252.1.252                                         1.907ms pmtu 1500 <---- Because the next hop MTU is 1500 bytes
 4:  38.104.162.77                                         1.390ms asymm  5
 5:  154.54.80.181                                         8.311ms asymm  6
 6:  154.54.46.178                                         8.491ms asymm  7
 7:  154.54.10.162                                         8.153ms asymm  8
 8:  216.6.81.28                                          26.726ms asymm 11
 9:  66.110.96.5                                          26.691ms asymm 11
10:  66.110.96.22                                         34.791ms asymm 15
11:  no reply
12:  no reply
13:  104.131.191.87                                       36.215ms reached
     Resume: pmtu 1500 hops 13 back 18
```


As can be seen in the ICMP reply packet decode, the router replying with the MTU exceeded is `128.252.100.126` (see notes with <---):

```
Internet Protocol Version 4, Src: 128.252.100.126, Dst: 128.252.5.113
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 56
    Identification: 0x0000 (0)
    Flags: 0x0000
        0... .... .... .... = Reserved bit: Not set
        .0.. .... .... .... = Don't fragment: Not set
        ..0. .... .... .... = More fragments: Not set
        ...0 0000 0000 0000 = Fragment offset: 0
    Time to live: 252
    Protocol: ICMP (1)
    Header checksum: 0x52dd [validation disabled]
    [Header checksum status: Unverified]
    Source: 128.252.100.126   <----- This router is where the path changes from 9000 bytes to 1500 bytes
    Destination: 128.252.5.113
Internet Control Message Protocol
    Type: 3 (Destination unreachable)
    Code: 4 (Fragmentation needed)
    Checksum: 0x5ee5 [correct]
    [Checksum Status: Good]
    Unused: 0000
    MTU of next hop: 1500 <---- Router sends the max MTU size of next hop
    Internet Protocol Version 4, Src: 128.252.5.113, Dst: 104.18.61.146
        0100 .... = Version: 4
        .... 0101 = Header Length: 20 bytes (5)
        Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
            0000 00.. = Differentiated Services Codepoint: Default (0)
            .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
        Total Length: 1528
        Identification: 0x0000 (0)
        Flags: 0x4000, Don't fragment
            0... .... .... .... = Reserved bit: Not set
            .1.. .... .... .... = Don't fragment: Set
            ..0. .... .... .... = More fragments: Not set
            ...0 0000 0000 0000 = Fragment offset: 0
        Time to live: 62
        Protocol: ICMP (1)
        Header checksum: 0x0af4 [validation disabled]
        [Header checksum status: Unverified]
        Source: 128.252.5.113
        Destination: 104.18.61.146
    Internet Control Message Protocol
        Type: 8 (Echo (ping) request)
        Code: 0
        Checksum: 0x1cf5 [unverified] [in ICMP error packet]
        [Checksum Status: Unverified]
        Identifier (BE): 29508 (0x7344)
        Identifier (LE): 17523 (0x4473)
        Sequence number (BE): 1 (0x0001)
        Sequence number (LE): 256 (0x0100)
```
