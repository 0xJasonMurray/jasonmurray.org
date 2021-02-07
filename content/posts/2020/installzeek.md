---
title: "Installing and running Zeek at home"
date: 2020-10-16T05:31:10-05:00
toc: false
images:
categories:
  - tech
tags: 
  - zeek
  - ubuntu
  - vm
---

# Overview

What are the devices in your house communicating with?  How many different services do your security cameras send information to?  Is your TV sending data to foreign countries?  What are the kids doing while your gone?   Are there any large file transfers happening to places you don't recognize?  Are there any open connections to SSH or RDP ports you are not aware of?

By TAPing and redirecting all traffic entering or leaving the network to a [Zeek](https://zeek.org/) server, we can start to answer these questions. 

What is Zeek?  [Zeek](https://zeek.org/) is an Open Source network security monitoring tool that ingests, analyzes, and logs all communication passing through it.  Logs can be broken out by connections, services, protocol, etc.  For example, a default installation logs individual connections, DNS queries, files transferred, SSL certificates, and more.  This is only the start, Zeek is fully customizable.  [Packages](https://packages.zeek.org/) are used to extend the analysis and logging capabilities of Zeek.

Example logs from a default installation:

```
jemurray@zeek:/usr/local/zeek/logs/current$ ls -al
total 4536
-rw-r--r-- 1 root root 1333909 Oct 15 13:48 conn.log
-rw-r--r-- 1 root root    2005 Oct 15 13:48 dhcp.log
-rw-r--r-- 1 root root 1335764 Oct 15 13:48 dns.log
-rw-r--r-- 1 root root  701709 Oct 15 13:48 files.log
-rw-r--r-- 1 root root   71498 Oct 15 13:48 http.log
-rw-r--r-- 1 root root   18520 Oct 15 13:47 notice.log
-rw-r--r-- 1 root root   51565 Oct 15 13:48 ntp.log
-rw-r--r-- 1 root root    4716 Oct 15 13:39 sip.log
-rw-r--r-- 1 root root   21228 Oct 15 13:48 ssh.log
-rw-r--r-- 1 root root  403803 Oct 15 13:48 ssl.log
-rw-r--r-- 1 root root    2967 Oct 15 13:46 weird.log
-rw-r--r-- 1 root root  507010 Oct 15 13:48 x509.log
```

Example of services flowing through my home network over the past hour:
```
jemurray@zeek:/usr/local/zeek/logs/current$ cat conn.log | ../../bin/zeek-cut -d service | sort | uniq -c | sort -rn
   4531 dns
   2731 -
   1276 ssl
    132 http
     98 ntp
     48 ssh
     14 dhcp
```

The top 10 DNS lookups over the past hour:
```
jemurray@zeek:/usr/local/zeek/logs/current$ cat dns.log | ../../bin/zeek-cut -d query | sort | uniq -c | sort -rn | head -10
    466 www.google.com
    245 google.com
    199 clients4.google.com
    146 api.smartthings.com
    128 _raop._tcp.local
     85 gateway.fe.apple-dns.net
     79 play.googleapis.com
     53 asimov.vortex.data.trafficmanager.net
     46 mvm.snapchat.com
     44 wirelessdevicestats.googleapis.com
```

The Zeek setup at my house looks like this and will be the basis for this tutorial:

![zeek home topology](/images/home-zeek-cluster.png)


I use the following hardware and software: 

- Ubuntu 20.04.x fresh install (ESXi VM)
- Copper 1Gbp/s TAP
- Latest Zeek software


# Details

## Install Ubuntu 20.04.x

Perform a standard Ubuntu 20.04.x installation.   

## ESXi VM Setup

Add a vSwitch:

- Change `Uplink 1` to the interface attached to the TAP
- Disable link discovery
- Don't worry about security here, we will adjust it in the port group

![vswitch add](/images/addvswitch.png)


Add a port group:

- Keep the VLAN 0
- Select the Zeek TAP virtual switch
- Set Promiscuous mode to Accept


![pg add](/images/addportgroup.png)


Attach the Zeek VM to the Zeek TAP switch and port group:

![set interface](/images/changezeekinterface.png)


Log into the Ubuntu server and test to see if TAP information is getting relayed properly:

```
jemurray@zeek:/usr/local/zeek/logs/current$ sudo tcpdump -n -i ens192 -c 10
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens192, link-type EN10MB (Ethernet), capture size 262144 bytes
13:17:37.057454 IP 193.122.241.6.8801 > 99.28.31.196.59888: UDP, length 315
13:17:37.057483 IP 193.122.241.6.8801 > 99.28.31.196.60422: UDP, length 114
13:17:37.064464 IP 99.28.31.196.60422 > 193.122.241.6.8801: UDP, length 887
13:17:37.068497 IP 193.122.241.6.8801 > 99.28.31.196.60422: UDP, length 479
13:17:37.077954 IP 193.122.241.6.8801 > 99.28.31.196.59888: UDP, length 312
13:17:37.077979 IP 193.122.241.6.8801 > 99.28.31.196.60422: UDP, length 590
13:17:37.088833 IP 193.122.241.6.8801 > 99.28.31.196.60422: UDP, length 764
13:17:37.098207 IP 193.122.241.6.8801 > 99.28.31.196.59888: UDP, length 310
13:17:37.098237 IP 193.122.241.6.8801 > 99.28.31.196.60422: UDP, length 1084
13:17:37.098241 IP 193.122.241.6.8801 > 99.28.31.196.60422: UDP, length 242
```



## Zeek Software Installation

Install dependencies:

```
sudo apt-get install cmake make gcc g++ flex bison libpcap-dev libssl-dev python-dev swig zlib1g-dev libmaxminddb-dev python3-dev
```

GeoLocation requires a MaxMind account to download the database.  This step is optional but recommended.  [Sign up for a MaxMind account](https://www.maxmind.com/en/geolite2/signup) then [download the GeoLite2 City database](https://www.maxmind.com/en/accounts/current/geoip/downloads).

Install the MaxMind Geo database:

```
tar xvf GeoLite2-City_20201013.tar.gz
sudo mkdir /usr/share/GeoIP
sudo mv GeoLite2-City_20201013/GeoLite2-City.mmdb /usr/share/GeoIP/
```

Download the Zeek software:

```
git clone --recursive https://github.com/zeek/zeek
```

Compile Zeek:

- Use python3 (it is 2020 after all)

```
cd zeek
./configure --with-python=/usr/bin/python3
make
sudo make install
```

Set the interface to monitor in `/usr/local/zeek/etc/node.cfg`. `ens192` is attached to the vSwitch which is attached to the copper tap on the ESXi host:

```
[zeek]
type=standalone
host=localhost
interface=ens192
```

Set the networks to monitor in `/usr/local/zeek/etc/networks.cfg`.  192.168.86.0/24 is the inside network:

```
192.168.86.0/24      Private IP space
```

Add Zeek binaries to the search path by adding this to the bottom of your `~/.profile`:

```
# set PATH so it includes Zeek bin if it exists
if [ -d "/usr/local/zeek/bin" ] ; then
    PATH="$PATH:/usr/local/zeek/bin"
fi
```

Finish install:

```
jemurray@zeek:/usr/local/zeek$ sudo bin/zeekctl
[sudo] password for jemurray:
Hint: Run the zeekctl "deploy" command to get started.

Welcome to ZeekControl 2.2.0

Type "help" for help.

[ZeekControl] > install
creating policy directories ...
installing site policies ...
generating standalone-layout.zeek ...
generating local-networks.zeek ...
generating zeekctl-config.zeek ...
generating zeekctl-config.sh ...
```

## Running Zeek

Start zeek:

```
[ZeekControl] > start
starting zeek ...
```


Check the running status:

```
[ZeekControl] > status
Name         Type       Host          Status    Pid    Started
zeek         standalone localhost     running   47117  15 Oct 01:23:00
```

Test to see if Zeek is logging:

```
jemurray@zeek:/usr/local/zeek/logs/current$ cat conn.log | ../../bin/zeek-cut -d ts id.orig_h id.resp_h id.resp_p service | head
2020-10-15T12:56:53+0000	99.28.31.196	35.185.69.233	443	ssl
2020-10-15T12:59:50+0000	99.28.31.196	8.8.8.8	53	dns
2020-10-15T12:59:53+0000	2600:1700:1391:411f:506d:fd48:cdb0:ae06	2620:149:a42:101::12	443	ssl
2020-10-15T12:59:51+0000	99.28.31.196	8.8.8.8	53	dns
2020-10-15T12:59:51+0000	99.28.31.196	8.8.8.8	53	dns
2020-10-15T12:59:57+0000	2600:1700:1391:411f:506d:fd48:cdb0:ae06	2607:f8b0:4009:805::200a	443	ssl
2020-10-15T12:59:52+0000	99.28.31.196	8.8.8.8	53	dns
2020-10-15T12:59:57+0000	2600:1700:1391:411f:506d:fd48:cdb0:ae06	2607:f8b0:4009:805::200a	443	ssl
2020-10-15T12:59:57+0000	99.28.31.196	17.253.23.202	80	http
2020-10-15T12:59:57+0000	2600:1700:1391:411f:506d:fd48:cdb0:ae06	2607:f8b0:4009:803::200a	443	ssl
```

## Looking at the log files

The log files are stored in:

```
/usr/local/zeek/logs
```

Now is the time to get to know your logs.  Look through the current and previous days logs.  Get a good understanding of what a "normal" day looks like.  This will be helpful when tracking down nefarious devices in the future.  

