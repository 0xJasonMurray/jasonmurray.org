---
title: "Monitoring layer-1 wireless traffic with Linux"
date: 2021-02-06T12:00:28-06:00
toc: false
images:
categories:
  - tech
tags: 
  - linux
  - iw
  - ip
  - wireless
  - tcpdump
  - monitor
description: Configuration guide to enable layer 1 wireless network monitoring in Linux.   
---

# Overview

Within Linux, a wireless adaptor operates in either [managed, AP, monitor, ad-hoc, WDS, or mesh mode](https://wireless.wiki.kernel.org/en/users/documentation/modes).  Managed mode enables the wireless card to connect to the network through an access point. This is the default and most common configuration for the majority of users.  Monitor mode, on the other hand, allows the network adaptor to passively monitor all traffic.  Using a packet capture tool such as `tcpdump`, we can extract and decode all wireless communication within range of the network adaptor.  

# Details

On the host in this example, `wlan0` is operating in `managed` mode connected to my home wireless network and `wlan1` an external USB wireless adaptor we will configure to operate in `monitor` mode.

Use `iw` to display the current operating mode of the wireless adaptor.  Network interfaces default to `managed` mode which allows them to connect to access points:

```
root@kali:~# iw wlan1 info
Interface wlan1
	ifindex 4
	wdev 0x100000001
	addr 3e:31:fc:bf:79:ba
	type managed
	wiphy 1
	txpower 20.00 dBm
```

Use `ip` to determine the current status of the link and ip address.  There is no need to configure an `ip address` on an interface in `monitor` mode:

```
root@kali:~# ip addr show dev wlan1
4: wlan1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 3e:31:fc:bf:79:ba brd ff:ff:ff:ff:ff:ff permaddr f4:6d:04:5d:d1:3b
```

When a wireless adaptor is in `managed` mode, packet capture tools are unable to extract packets not destined for the interface.  We confirm this by enabling the adaptor and attempting a packet capture with `tcpdump`.  There are no captured packets:

```
root@kali:~# ip link set wlan1 up
root@kali:~# sudo tcpdump -i wlan1 -n
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type EN10MB (Ethernet), capture size 262144 bytes
^C
0 packets captured
0 packets received by filter
0 packets dropped by kernel
```

Switch the interface from `managed` to `monitor` mode and switch the channel to `149` (my home network):

```
root@kali:~# ip link set wlan1 down
root@kali:~# iw wlan1 set monitor control
root@kali:~# iw wlan1 set channel 149
root@kali:~# ip link set wlan1 up
```

Validate the `wlan1` interface is active on channel `149`:

```
root@kali:~# iw wlan1 info
Interface wlan1
	ifindex 5
	wdev 0x200000001
	addr 16:60:c5:85:b2:63
	type monitor
	wiphy 2
	channel 149 (5745 MHz), width: 20 MHz (no HT), center1: 5745 MHz
	txpower 20.00 dBm
```

**Side Note:** Wireless cards can only monitor one channel at a time.  To monitor more then one channel at a time, additional network cards are needed.  About 10 years ago I put together a laptop with external network adaptors and [this script](https://github.com/0xJasonMurray/publicScripts/blob/master/wifi-monitor.sh) for just such an occasion:

![Image of a laptop with 7 USB wireless adaptors attached](/images/2021-02-02-20-13-09.png)

Run `tcpdump` with the interface in `monitor` mode.  We are now capturing the layer 1 (IEEE802_11_RADIO) traffic in the air:

```
root@kali:~# sudo tcpdump -i wlan1 -n -c 10
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
12:12:47.062667 6.0 Mb/s 5745 MHz 11a -57dBm signal antenna 1 Request-To-Send TA:5b:cb:52:d8:fc:65
12:12:47.062682 6.0 Mb/s 5745 MHz 11a -47dBm signal antenna 1 Clear-To-Send RA:5a:cb:52:d8:fc:65
12:12:47.062840 24.0 Mb/s 5745 MHz 11a -47dBm signal antenna 1 BA RA:5a:cb:52:d8:fc:65
12:12:47.064084 24.0 Mb/s 5745 MHz 11a -23dBm signal antenna 1 Data IV:299 Pad 20 KeyID 0
12:12:47.064202 24.0 Mb/s 5745 MHz 11a -37dBm signal antenna 1 Acknowledgment RA:10:02:b5:b5:8c:72
12:12:47.064296 24.0 Mb/s 5745 MHz 11a -23dBm signal antenna 1 Data IV:29a Pad 20 KeyID 0
12:12:47.064404 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Acknowledgment RA:10:02:b5:b5:8c:72
12:12:47.064561 24.0 Mb/s 5745 MHz 11a -23dBm signal antenna 1 Data IV:29b Pad 20 KeyID 0
12:12:47.064616 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Acknowledgment RA:10:02:b5:b5:8c:72
12:12:47.064793 24.0 Mb/s 5745 MHz 11a -23dBm signal antenna 1 Data IV:29c Pad 20 KeyID 0
10 packets captured
16 packets received by filter
0 packets dropped by kernel
109 packets dropped by interface
```

To find a specific device, capture packets on `wlan1`, filtering for the hardware MAC address `f0:18:98:9b:b5:b2`:

```
root@kali:~# sudo tcpdump -i wlan1 -n -c 10 'wlan addr1 f0:18:98:9b:b5:b2'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
12:15:39.511482 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:f0:18:98:9b:b5:b2
12:15:39.511540 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:f0:18:98:9b:b5:b2
12:15:39.511677 24.0 Mb/s 5745 MHz 11a -43dBm signal antenna 1 BA RA:f0:18:98:9b:b5:b2
12:15:39.516845 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:f0:18:98:9b:b5:b2
12:15:39.516990 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:f0:18:98:9b:b5:b2
12:15:39.519958 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:f0:18:98:9b:b5:b2
12:15:39.520093 24.0 Mb/s 5745 MHz 11a -43dBm signal antenna 1 BA RA:f0:18:98:9b:b5:b2
12:15:39.523620 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:f0:18:98:9b:b5:b2
12:15:39.523678 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:f0:18:98:9b:b5:b2
12:15:39.527729 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:f0:18:98:9b:b5:b2
10 packets captured
17 packets received by filter
0 packets dropped by kernel
46 packets dropped by interface
```