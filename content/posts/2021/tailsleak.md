---
title: "Caution: MAC address information leaked when booting Tails Linux on a MacBook Pro (not Tails fault)"
date: 2021-02-11T12:34:47-06:00
toc: false
images:
categories:
  - tech
tags:
  - tails
  - linux
  - macaddress
  - leaked
  - security
  - detection
description: Be careful booting Tails Linux on a MacBook Pro.  Before Tails begins the boot process, a MacBook Pro leaks the real, non-randomized, MAC address over the wireless interface.  
---

## Overview

While exploring the interworkings of the [Tails MAC address randomization process](https://tails.boum.org/doc/first_steps/welcome_screen/mac_spoofing/index.en.html#:~:text=Tails%20can%20temporarily%20change%20the,are%2C%20to%20the%20local%20network.), I discovered a situation where the real MAC address is leaked if Tails is booted from a USB drive on a MacBook Pro.

## Details

I started off by building a Linux workstation with the `wlan1` interface [in monitoring mode](/posts/2021/monitorwireless/).  The wireless sniffer was setup to capture traffic from the real MAC address (`ac:bc:32:b4:a9:83`) of the MacBook Pro on channel 149.  As a baseline, I am able to confirm traffic is received from the MacBook Pro's `en0` network adaptor while booted in macOS:

```
root@kali:~# sudo tcpdump -i wlan1 -n -c 10 'wlan addr1 ac:bc:32:b4:a9:83'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
12:34:04.627715 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:b4:a9:83
12:34:04.627853 24.0 Mb/s 5745 MHz 11a -43dBm signal antenna 1 BA RA:ac:bc:32:b4:a9:83
12:34:04.628753 24.0 Mb/s 5745 MHz 11a -37dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:b4:a9:83
12:34:04.629066 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:ac:bc:32:b4:a9:83
12:34:04.637348 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:b4:a9:83
12:34:04.637493 24.0 Mb/s 5745 MHz 11a -43dBm signal antenna 1 BA RA:ac:bc:32:b4:a9:83
12:34:04.638673 6.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Request-To-Send TA:59:cb:52:d8:fc:24
12:34:04.651388 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:b4:a9:83
12:34:04.651893 24.0 Mb/s 5745 MHz 11a -43dBm signal antenna 1 BA RA:ac:bc:32:b4:a9:83
12:34:04.651934 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:ac:bc:32:b4:a9:83
10 packets captured
19 packets received by filter
0 packets dropped by kernel
182 packets dropped by interface
```

Next, Tails Linux is booted from a USB drive attached to the MacBook Pro.  

To boot from a USB drive, hold down the `option + power-button`.  The initial screen will display the options for booting into the internal macOS drive or the external EFI tails USB drive.  

The boot screen also displays the option to connect to a wireless network.   This is the point where the MacBook Pro begins to leak the **real** MAC address of the built in wireless adaptor.  To be clear, this is **NOT** a flaw in Tails.  The MacBook Pro's boot firmware is the system sending the wifi `probes`:  

![Image of Tails boot selection on MacBook PRO](/images/tailsbootmacbookpro.jpg)

Even if no network is selected in the menu above, the MacBook Pro sends out wireless `probe` requests.  Since Tails has not booted, the MAC randomization process has not started.  The real MAC address of the laptop is being leaked.  If you are trying to remain anonymous, this will be a problem:

```
root@kali:~# sudo tcpdump -i wlan1 -n  'wlan addr1 ac:bc:32:b4:a9:83'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
12:38:14.822647 6.0 Mb/s 5745 MHz 11a -55dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:14.833179 6.0 Mb/s 5745 MHz 11a -43dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:14.833599 6.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:14.842630 6.0 Mb/s 5745 MHz 11a -55dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:14.842945 6.0 Mb/s 5745 MHz 11a -45dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:14.843610 6.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:24.821288 6.0 Mb/s 5745 MHz 11a -53dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:24.821633 6.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:24.822160 6.0 Mb/s 5745 MHz 11a -45dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:24.841903 6.0 Mb/s 5745 MHz 11a -55dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
12:38:24.852982 6.0 Mb/s 5745 MHz 11a -47dBm signal antenna 1 Probe Response (msecure) [6.0* 9.0 12.0* 18.0 24.0* 36.0 48.0 54.0 Mbit] CH: 149, PRIVACY
```

As Tails begins the boot process, the MacBook Pro stops sending the `probes`.  No more packets are leaked:

```
root@kali:~# sudo tcpdump -i wlan1 -n -c 10 'wlan addr1 ac:bc:32:b4:a9:83'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
^C
0 packets captured
3 packets received by filter
0 packets dropped by kernel
41 packets dropped by interface
```

Once Tails is booted, the MAC address is randomized to `ac:bc:32:76:77:73`.  Capturing with this filter displays packets as expected:

```
root@kali:~# sudo tcpdump -i wlan1 -n -c 10 'wlan addr1 ac:bc:32:76:77:73'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
12:51:00.898780 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:76:77:73
12:51:00.898909 12.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:ac:bc:32:76:77:73
12:51:05.396286 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:76:77:73
12:51:05.396310 12.0 Mb/s 5745 MHz 11a -37dBm signal antenna 1 BA RA:ac:bc:32:76:77:73
12:51:05.539507 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:76:77:73
12:51:05.539527 12.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:ac:bc:32:76:77:73
12:51:05.622892 24.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:76:77:73
12:51:05.623059 12.0 Mb/s 5745 MHz 11a -39dBm signal antenna 1 BA RA:ac:bc:32:76:77:73
12:51:05.684138 24.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 Clear-To-Send RA:ac:bc:32:76:77:73
12:51:05.684301 12.0 Mb/s 5745 MHz 11a -41dBm signal antenna 1 BA RA:ac:bc:32:76:77:73
10 packets captured
19 packets received by filter
0 packets dropped by kernel
72 packets dropped by interface
```

It is worth noting, even with MAC address randomization enabled, Tails keeps the hardware vendor OUI in place.  If the Tails device is the only Apple in the room, it will be a dead give away.  This behavior is [clearly documented on the Tails website](https://tails.boum.org/contribute/design/MAC_address/#limitation-only-spoof-nic-part):


```
root@kali:~# wget -qO- https://api.macvendors.com/ac:bc:32:76:77:73
Apple, Inc.
```


To summarize, combining the initial leaking of the full MAC address and the randomized MAC address Tails generates, enough data points may be present to link the device to a specific person.  If your goal is anonymity with plausible deniability, be careful booting Tails Linux on a MacBook Pro.  The same Tails USB drive booted on a Dell laptop does not exhibit the MAC leaking behavior.  

