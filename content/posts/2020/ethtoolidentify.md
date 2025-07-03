---
title: "Locating ethernet ports with ethtool"
date: 2020-12-01T06:24:01-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ethtool
  - ethernet
  - networking
  - linux
---

# TL;DR

Use `ethtool` to locate a physical network port by blinking the lights:

```
sudo ethtool --identify eth0
```

![](/images/2020-12-01-13-18-58.png)


# Details


On systems with multiple physical network adaptors, it is not always obvious which physical port belongs to the software interface.  For example, the following desktop has multiple network interfaces:

```
jemurray@desktop:~$ ifconfig -a
eth0      Link encap:Ethernet  HWaddr 78:e3:b5:06:91:18
          inet addr:192.168.86.123  Bcast:192.168.86.255  Mask:255.255.255.0
          inet6 addr: fe80::7ae3:b5ff:fe06:9118/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:9613 errors:0 dropped:172 overruns:0 frame:0
          TX packets:1256 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:798122 (798.1 KB)  TX bytes:338757 (338.7 KB)
          Interrupt:95

eth1      Link encap:Ethernet  HWaddr 78:e3:b5:06:91:19
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          Interrupt:99

eth2      Link encap:Ethernet  HWaddr 78:e3:b5:06:91:1a
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          Interrupt:103

eth3      Link encap:Ethernet  HWaddr 78:e3:b5:06:91:1b
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          Interrupt:107
```

The `ethtool` command has an `--identify` option that blinks the lights on a physical network interface.


Before the `ethtool` command is run:

![](/images/2020-12-01-13-21-32.png)


Using `ethtool` to locate the port:

```
sudo ethtool --identify eth0
```


![](/images/2020-12-01-13-18-58.png)


Unfortunatly, not all hardware supports identification:

```
jemurray@server:~$ sudo ethtool --identify eth0
Cannot identify NIC: Operation not supported
```