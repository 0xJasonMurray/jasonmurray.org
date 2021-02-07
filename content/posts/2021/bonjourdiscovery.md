---
title: "Using dns-sd to discovery Bonjour advertised devices on a local network"
date: 2021-01-07T16:15:21-06:00
toc: false
images:
categories:
  - tech
tags: 
  - dns-sd
  - bonjour
  - mdns
---

# Overview

Zero-configuration networking (zeroconf) is a suite of technologies used to configure, distribute, and discover devices (ie. laptops) and services (ie. printers) on a local network.  Many different protocol implementations of zeroconf exist depending on the operating system or vendor of the device.   The most common implementations are Apples Bonjour and Microsofts LLMNR, both inspired by the Multicast Domain Name Service.

Discovery tools like `dns-sd` on macOS, allow us to find and examine devices connected to a local network.

# Details

Query for all devices advertising services by using the `dns-sd` command with the `-B` (browse) option:

```
jemurray@jasons-mbp:~ $ dns-sd  -B  _services._dns-sd._udp
Browsing for _services._dns-sd._udp
DATE: ---Thu 07 Jan 2021---
16:13:54.194  ...STARTING...
Timestamp     A/R    Flags  if Domain               Service Type         Instance Name
16:13:54.194  Add        3   1 .                    _udp.local.          _OZOmniGraffle7
16:13:54.194  Add        3   1 .                    _tcp.local.          _companion-link
16:13:54.194  Add        3   6 .                    _udp.local.          _OZOmniGraffle7
16:13:54.194  Add        3   6 .                    _tcp.local.          _companion-link
16:13:54.194  Add        3   6 .                    _tcp.local.          _googlerpc
16:13:54.194  Add        3   6 .                    _tcp.local.          _googlecast
16:13:54.194  Add        3   6 .                    _tcp.local.          _googlezone
16:13:54.194  Add        3   6 .                    _udp.local.          _sleep-proxy
16:13:54.194  Add        3   6 .                    _tcp.local.          _mediaremotetv
16:13:54.194  Add        3   6 .                    _tcp.local.          _raop
16:13:54.194  Add        3   6 .                    _tcp.local.          _homekit
16:13:54.194  Add        3   6 .                    _tcp.local.          _airplay
16:13:54.194  Add        3   6 .                    _tcp.local.          _hap
16:13:54.194  Add        3   6 .                    _tcp.local.          _pdl-datastream
16:13:54.194  Add        3   6 .                    _tcp.local.          _ipp
16:13:54.195  Add        3   6 .                    _tcp.local.          _http
16:13:54.195  Add        3   6 .                    _tcp.local.          _scanner
16:13:54.195  Add        3   6 .                    _tcp.local.          _http-alt
16:13:54.195  Add        3   6 .                    _tcp.local.          _uscan
16:13:54.195  Add        3   6 .                    _tcp.local.          _ipps
16:13:54.195  Add        3   6 .                    _tcp.local.          _privet
16:13:54.195  Add        3   6 .                    _tcp.local.          _uscans
16:13:54.195  Add        2   6 .                    _tcp.local.          _rdlink
```

Gather additional information about a specific group of devices, in this case `_ipp` or printers.  Take the `Instance Name` from the list above and append it to `Service Type`, creating the command:

```
jemurray@jasons-mbp:~ $ dns-sd  -B  _ipps._tcp
Browsing for _ipps._tcp
DATE: ---Thu 07 Jan 2021---
16:16:17.565  ...STARTING...
Timestamp     A/R    Flags  if Domain               Service Type         Instance Name
16:16:17.566  Add        2   6 local.               _ipps._tcp.          HP ENVY 4520 series [20FBDE]
```

The `-L` (lookup) option displays detailed information necessary to connect to the device such as hostname or port numbers:

```
jemurray@jasons-mbp:~ $ dns-sd -L "HP ENVY 4520 series [20FBDE]" _ipps._tcp.
Lookup HP ENVY 4520 series [20FBDE]._ipps._tcp..local
DATE: ---Thu 07 Jan 2021---
16:23:50.176  ...STARTING...
16:23:50.534  HP\032ENVY\0324520\032series\032[20FBDE]._ipps._tcp.local. can be reached at HP98E7F420FBDE.local.:443 (interface 6)
 txtvers=1 qtotal=1 pdl=application/vnd.hp-PCL,image/jpeg,application/PCLm,image/urf,image/pwg-raster rp=ipp/print PaperMax=legal-A4 kind=document,envelope,photo,postcard URF=CP1,MT1-2-8-9-10-11,PQ3-4-5,RS300,SRGB24,OB9,OFU0,W8-16,DEVW8-16,DEVRGB24-48,ADOBERGB24-48,DM3,IS1,V1.4 ty=HP\ ENVY\ 4520\ series product=\(HP\ ENVY\ 4520\ series\) usb_MFG=HP usb_MDL=ENVY\ 4520\ series priority=30 mac=98:e7:f4:20:fb:de adminurl=http://HP98E7F420FBDE.local./#hId-pgAirPrint note= mopria-certified=1.3 TLS=1.2 Fax=F UUID=1c852a4d-b800-1f08-abcd-98e7f420fbde Color=T Duplex=T Scan=T
```

Finally, dump the IP address of the device:

```
jemurray@jasons-mbp:~ $ dns-sd -Gv4v6 HP98E7F420FBDE.local
DATE: ---Thu 07 Jan 2021---
16:24:43.646  ...STARTING...
Timestamp     A/R    Flags if Hostname                               Address                                      TTL
16:24:43.647  Add 40000003  6 HP98E7F420FBDE.local.                  FE80:0000:0000:0000:9AE7:F4FF:FE20:FBDE%en0  120
16:24:43.647  Add 40000003  6 HP98E7F420FBDE.local.                  2600:1700:1391:411F:9AE7:F4FF:FE20:FBDE%<0>  120
16:24:43.647  Add 40000002  6 HP98E7F420FBDE.local.                  192.168.86.156                               120
```


Oh yea, and happy birthday to me.