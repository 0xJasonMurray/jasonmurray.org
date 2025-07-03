---
title: "Persistent network configuration storage within Tails Linux may reveal historical location data"
date: 2021-02-17T13:39:04-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tails
  - identity
  - anonymous
  - security
description: Enabling persistent network storage in Tails linux may reveal historical location data.  Saved networks broadcast a `probe` packet as the operating system boots up.  It is possible to passively estimate a devices previous locations using this information.
---

## Overview

Enabling persistent network storage in Tails linux may reveal historical location data.  Saved networks broadcast a `probe` packet as the operating system boots up.  It is possible to passively estimate a devices previous locations using this information.

In a [previous post](/posts/2021/tailsleak/) I documented an issue where a MacBook Pro will leak the real MAC address of the laptop when booting the Tails operating system from a USB drive; Thereby defeating the MAC randomization feature of Tails during initial power up.  It's important to note, the MAC address leak is a "feature" of the macOS boot process and not the fault of Tails.

This started me thinking of other ways identity may be leaked from the wireless network.   In this post, I will show how persistent networking storage may leak wireless network information which has the potential to reveal enough information to tie a device to a location.

## Scenario Setup

A user enabled persistent storage in order to save various settings, most important for this example, Network Connections.  The user moves between their home network and the local coffee shop.  The home network SSID is `aNetworkThatGivesAwayMyIdentity` and the coffee shop SSID is `aCoffeeShop`.   Both of these wireless networks are saved in the persistent storage settings within Tails.

## Leaking Information

When Tails boots, a pre-boot menu prompts the user to unlock the Persistent Storage vault. Once complete the full boot process continues.

As soon as Tails gets to the point where the networking interfaces are brought up, an 802.11 wireless `Probe Request` is made for all networks saved in the persistent storage vault.  In this example, two probes for `aNetworkThatGivesAwayMyIdentity` and `aCoffeeShop`, they look like this:

```
13:34:20.852748 6.0 Mb/s 5745 MHz 11a -29dBm signal antenna 1 BSSID:ff:ff:ff:ff:ff:ff DA:ff:ff:ff:ff:ff:ff SA:8a:0d:51:06:96:5a Probe Request (aNetworkThatGivesAwayMyIdentity) [6.0 9.0 12.0 18.0 24.0 36.0 48.0 54.0 Mbit]
```

```
13:34:21.027884 6.0 Mb/s 5745 MHz 11a -77dBm signal antenna 1 BSSID:ff:ff:ff:ff:ff:ff DA:ff:ff:ff:ff:ff:ff SA:1c:f2:9a:62:e8:9c Probe Request (aCoffeeShop) [6.0 9.0 12.0 18.0 24.0 36.0 48.0 54.0 Mbit]
```

The full output from `tcpdump` used to capture `probe` packets:


```
root@kali:~# sudo tcpdump -i wlan1 -n -e -s0 type mgt subtype probe-req
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan1, link-type IEEE802_11_RADIO (802.11 plus radiotap header), capture size 262144 bytes
13:34:20.852748 6.0 Mb/s 5745 MHz 11a -29dBm signal antenna 1 BSSID:ff:ff:ff:ff:ff:ff DA:ff:ff:ff:ff:ff:ff SA:8a:0d:51:06:96:5a Probe Request (aNetworkThatGivesAwayMyIdentity) [6.0 9.0 12.0 18.0 24.0 36.0 48.0 54.0 Mbit]
13:34:20.873211 6.0 Mb/s 5745 MHz 11a -29dBm signal antenna 1 BSSID:ff:ff:ff:ff:ff:ff DA:ff:ff:ff:ff:ff:ff SA:8a:0d:51:06:96:5a Probe Request (aNetworkThatGivesAwayMyIdentity) [6.0 9.0 12.0 18.0 24.0 36.0 48.0 54.0 Mbit]
13:34:21.027884 6.0 Mb/s 5745 MHz 11a -77dBm signal antenna 1 BSSID:ff:ff:ff:ff:ff:ff DA:ff:ff:ff:ff:ff:ff SA:1c:f2:9a:62:e8:9c Probe Request (aCoffeeShop) [6.0 9.0 12.0 18.0 24.0 36.0 48.0 54.0 Mbit]
13:34:22.798768 6.0 Mb/s 5745 MHz 11a -33dBm signal antenna 1 BSSID:ff:ff:ff:ff:ff:ff DA:ff:ff:ff:ff:ff:ff SA:ac:bc:32:b0:dd:83 Probe Request (aCoffeeShop) [6.0 9.0 12.0 18.0 24.0 36.0 48.0 54.0 Mbit]
```

## Why or how is this a problem?

Tails takes special care to keep identity private.  Leaking wireless network `probe` packets with a network name, could tie a device back to a specific location, giving away a users movements and location history.  For example, if I was in a coffee shop and an adversary was trying to determine where I lived or visited.  They could look at all the broadcasted `probe` packets and compare it with public wifi data on [wigle.net](https://wigle.net/) to pinpoint exact locations my device moved between.