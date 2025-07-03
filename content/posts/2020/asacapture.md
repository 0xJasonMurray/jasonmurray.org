---
title: "Data-plane packet captures on the Cisco ASA"
date: 2020-08-20T09:54:27-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - asa
  - capture
---

The `capture` command is used to view data-plane traffic flowing through a Cisco ASA.  This command can be run safely during production hours with minimal performance impact to the ASA.

In this example, a capture is started on the `outside` and `inside` interface to confirm the policy either allows or blocks the flow.

```
capture DUOout circular-buffer interface outside match ip 192.168.200.1 255.255.255.255 192.168.100.1 255.255.255.255
 
capture DUOin circular-buffer interface inside match ip 192.168.200.1 255.255.255.255 192.168.100.1 255.255.255.255
```

Show the running captures:

```
cisco-asa-fw/red# show capture
capture DUOout type raw-data interface outside circular-buffer [Capturing - 38314 bytes]
  match ip host 192.168.200.1 host 192.168.100.1
capture DUOin type raw-data interface inside circular-buffer [Capturing - 36042 bytes]
  match ip host 192.168.200.1 host 192.168.100.1
```

View the packets captured:

```
cisco-asa-fw/red# show capture DUOin

8 packets captured

   1: 09:41:12.535281       802.1Q vlan#2 P0 192.168.100.1 > 192.168.200.1: icmp: echo request
   2: 09:41:12.535418       802.1Q vlan#2 P0 192.168.100.1 > 192.168.200.1: icmp: echo request
   3: 09:41:12.536562       802.1Q vlan#2 P0 192.168.200.1 > 192.168.100.1: icmp: echo reply
   4: 09:41:12.536593       802.1Q vlan#2 P0 192.168.200.1 > 192.168.100.1: icmp: echo reply
   5: 09:41:42.535159       802.1Q vlan#2 P0 192.168.100.1 > 192.168.200.1: icmp: echo request
   6: 09:41:42.535311       802.1Q vlan#2 P0 192.168.100.1 > 192.168.200.1: icmp: echo request
   7: 09:41:42.537173       802.1Q vlan#2 P0 192.168.200.1 > 192.168.100.1: icmp: echo reply
   8: 09:41:42.537188       802.1Q vlan#2 P0 192.168.200.1 > 192.168.100.1: icmp: echo reply
8 packets shown
```

Stop the packet captures when finished:

```
no capture DUOout type raw-data interface outside circular-buffer
no capture DUOin type raw-data interface outside circular-buffer
```


