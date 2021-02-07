---
title: "How to fix Microsoft Teams poor audio and video over VPN"
date: 2020-09-22T13:36:57-05:00
toc: false
images:
categories:
  - tech
tags: 
  - vpn
  - team
  - microsoft
  - cisco
---

# Overview

Microsoft Teams is experiencing poor audio and video problems when clients are connected to the Cisco AnyConnect VPN gateway.  We are able to consistently reproduce this problem by establishing a call between two users using the `chat` option in Teams.  Both users must be connected to the same VPN gateway.  

The problem does **NOT** exist when a call is established through a `meeting` or when multiple users are in a group call. Only singleuser-to-singleuser calls experience poor connection quality.

# Resolution Summary

On the AnyConnect VPN, create a dACL to block UDP ports 50000-59999 to all client VPN IP subnets.  This will force the clients to communicate through a Microsoft proxy.  Clients are no longer able to communicate directly with each other.

# Client and VPN Information

- VPN gateway: Cisco 1420 ASA Version 9.12(3)12
- Source Client IP: 192.168.0.1 (VPN Connected)
- Destination Client IP: 192.168.0.2 (VPN Connected)

# Details

Poor call quality happens when the STUN process determines the two clients are able to establish a direct point-to-point connection.  

The clients use the STUN protocol to determine if direct communication is possible:

```
$ tshark -r teams-chat-poor-quality.pcapng -Y 'frame.number>=804' | head -2
  804  16.558653 192.168.0.1 → 192.168.0.2 STUN 148 Binding Request user: abcd:nxdf
  805  16.558741 192.168.0.1 → 192.168.0.2 STUN 120 Binding Success Response XOR-MAPPED-ADDRESS: 192.168.0.2:50035
```

After the STUN process completes, clients establish a point-to-point connection.  When clients are able to communicate directly, the call quality is poor:

```
$ tshark -r teams-chat-poor-quality.pcapng -Y 'frame.number>=985' | head -10
  985  19.513739 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  986  19.513809 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  987  19.513864 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  988  19.513914 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  989  19.513964 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  990  19.514015 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  991  19.514069 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  992  19.514115 192.168.0.1 → 192.168.0.2 UDP 1101 50032 → 50035 Len=1069
  993  19.514166 192.168.0.1 → 192.168.0.2 UDP 1096 50032 → 50035 Len=1064
```


To resolve the problem, we configured a dACL that blocks the client VPN IP subnets from making connections over UDP 50000-59999 with each other:

```
deny udp any 192.168.0.0 255.255.255.0 range 50000 59999
permit ip any any
```

When the client attempts to make a STUN to the other client, the dACL blocks the request:

```
$ tshark -r teams-chat-blockedSTUN50000-59999-goodquality.pcapng -Y 'frame.number==1122 || frame.number==1131'
 1122  12.957642 192.168.0.1 → 192.168.0.2 STUN 144 Binding Request user: abcd:dqVc
 1131  13.261219 192.168.0.1 → 192.168.0.2 STUN 144 Binding Request user: abcd:dqVc
```

After the point-to-point STUN fails, a new STUN process maps a connection to the Microsoft proxy:

```
Frame 1553: 120 bytes on wire (960 bits), 120 bytes captured (960 bits) on interface utun5, id 0
Null/Loopback
Internet Protocol Version 4, Src: 52.114.157.102, Dst: 192.168.0.1
User Datagram Protocol, Src Port: 3480, Dst Port: 50030
Session Traversal Utilities for NAT
    Message Type: 0x0101 (Binding Success Response)
        .... ...1 ...0 .... = Message Class: 0x10 Success Response (2)
        ..00 000. 000. 0001 = Message Method: 0x0001 Binding (0x001)
        ..0. .... .... .... = Message Method Assignment: IETF Review (0x0)
    Message Length: 68
    Message Cookie: 2112a442
    Message Transaction ID: da1f80952cecba4b85573e5b
    Attributes
        MS-IMPLEMENTATION-VERSION: Unknown (0x7)
            Attribute Type: MS-IMPLEMENTATION-VERSION (0x8070)
                1... .... .... .... = Attribute Type Comprehension: Optional (0x1)
                .0.. .... .... .... = Attribute Type Assignment: IETF Review (0x0)
            Attribute Length: 4
            MS Version: Unknown (7)
        XOR-MAPPED-ADDRESS: 192.168.175.65:50030
            Attribute Type: XOR-MAPPED-ADDRESS (0x0020)
                0... .... .... .... = Attribute Type Comprehension: Required (0x0)
                .0.. .... .... .... = Attribute Type Assignment: IETF Review (0x0)
            Attribute Length: 8
            Reserved: 00
            Protocol Family: IPv4 (0x01)
            Port (XOR-d): e27c
            [Port: 50030]
            IP (XOR-d): a1ee0b03
            [IP: 192.168.175.65]
```


When the client-to-client STUN process fails, Teams establishes a connection to a Microsoft server instead of directly to the client.  Audio and video quality are good after implementing the dACL:

```
$ tshark -r teams-chat-blockedSTUN50000-59999-goodquality.pcapng  -Y 'frame.number>=1572' | head -10
 1572  17.961318 192.168.0.1 → 52.114.157.102 UDP 76 50030 → 3480 Len=44
 1573  17.961397 192.168.0.1 → 52.114.157.102 UDP 1119 50030 → 3480 Len=1087
 1574  17.961474 192.168.0.1 → 52.114.157.102 UDP 1119 50030 → 3480 Len=1087
 1575  17.961545 192.168.0.1 → 52.114.157.102 UDP 1119 50030 → 3480 Len=1087
 1576  17.961587 192.168.0.1 → 52.114.157.102 UDP 1119 50030 → 3480 Len=1087
 1577  17.961626 192.168.0.1 → 52.114.157.102 UDP 1119 50030 → 3480 Len=1087
 1578  17.961694 192.168.0.1 → 52.114.157.102 UDP 1119 50030 → 3480 Len=1087
```
