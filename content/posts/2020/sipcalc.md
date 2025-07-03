---
title: "CLI based subnet calculator"
date: 2020-07-22T12:18:02-05:00
toc: false
images:
categories:
  - tech
tags: 
  - sipcalc
  - vlsm
  - subnets
  - networking
---

My favorite subnet calculator is the terminal based command [sipcalc](http://www.routemeister.net/projects/sipcalc/index.html).   While it is no longer under active development, it has every feature a network engineer could need.


Features (IPv4)

- Multiple address and netmask input formats.
- Retrieving of address information from interfaces.
- Classfull and CIDR output.
- Multiple address and netmask output formats (dotted quad, hex, number of bits).
- Output of broadcast address, network class, Cisco wildcard, hosts/range, network range.
- Output of multiple types of bitmaps.
- Output of a user-defined number of extra networks.
- Multiple networks input from commandline.
- DNS resolutions of hostnames.
- Parsing of a newline separated list of networks from standard input (STDIN).
- The ability to "split" a network based on a smaller netmask, also with recursive runs on the gener. 

Features (IPv6)

- Compressed and expanded input addresses.
- Compressed and expanded output.
- Standard IPv6 network output.
- v4 in v6 output.
- Reverse DNS address generation.
- DNS resolutions of hostnames.
- The ability to "split" a network based on a smaller netmask, also with recursive runs on the generated subnets. 


IPv4 example:

```
jemurray@shell:~$ sipcalc 192.168.0.0/24
-[ipv4 : 192.168.0.0/24] - 0

[CIDR]
Host address            - 192.168.0.0
Host address (decimal)  - 3232235520
Host address (hex)      - C0A80000
Network address         - 192.168.0.0
Network mask            - 255.255.255.0
Network mask (bits)     - 24
Network mask (hex)      - FFFFFF00
Broadcast address       - 192.168.0.255
Cisco wildcard          - 0.0.0.255
Addresses in network    - 256
Network range           - 192.168.0.0 - 192.168.0.255
Usable range            - 192.168.0.1 - 192.168.0.254
```

Simple IPv6 example:

```
jemurray@shell:~$ sipcalc 2001:0002::/48
-[ipv6 : 2001:0002::/48] - 0

[IPV6 INFO]
Expanded Address        - 2001:0002:0000:0000:0000:0000:0000:0000
Compressed address      - 2001:2::
Subnet prefix (masked)  - 2001:2:0:0:0:0:0:0/48
Address ID (masked)     - 0:0:0:0:0:0:0:0/48
Prefix address          - ffff:ffff:ffff:0:0:0:0:0
Prefix length           - 48
Address type            - Aggregatable Global Unicast Addresses
Network range           - 2001:0002:0000:0000:0000:0000:0000:0000 -
                          2001:0002:0000:ffff:ffff:ffff:ffff:ffff
```
Splitting a /24 into /29's:

```
jemurray@shell:~$ sipcalc -s /29 192.168.0.0/24
-[ipv4 : 192.168.0.0/24] - 0

[Split network]
Network                 - 192.168.0.0     - 192.168.0.7
Network                 - 192.168.0.8     - 192.168.0.15
Network                 - 192.168.0.16    - 192.168.0.23
Network                 - 192.168.0.24    - 192.168.0.31
Network                 - 192.168.0.32    - 192.168.0.39
Network                 - 192.168.0.40    - 192.168.0.47
Network                 - 192.168.0.48    - 192.168.0.55
Network                 - 192.168.0.56    - 192.168.0.63
Network                 - 192.168.0.64    - 192.168.0.71
Network                 - 192.168.0.72    - 192.168.0.79
Network                 - 192.168.0.80    - 192.168.0.87
Network                 - 192.168.0.88    - 192.168.0.95
Network                 - 192.168.0.96    - 192.168.0.103
Network                 - 192.168.0.104   - 192.168.0.111
Network                 - 192.168.0.112   - 192.168.0.119
Network                 - 192.168.0.120   - 192.168.0.127
Network                 - 192.168.0.128   - 192.168.0.135
Network                 - 192.168.0.136   - 192.168.0.143
Network                 - 192.168.0.144   - 192.168.0.151
Network                 - 192.168.0.152   - 192.168.0.159
Network                 - 192.168.0.160   - 192.168.0.167
Network                 - 192.168.0.168   - 192.168.0.175
Network                 - 192.168.0.176   - 192.168.0.183
Network                 - 192.168.0.184   - 192.168.0.191
Network                 - 192.168.0.192   - 192.168.0.199
Network                 - 192.168.0.200   - 192.168.0.207
Network                 - 192.168.0.208   - 192.168.0.215
Network                 - 192.168.0.216   - 192.168.0.223
Network                 - 192.168.0.224   - 192.168.0.231
Network                 - 192.168.0.232   - 192.168.0.239
Network                 - 192.168.0.240   - 192.168.0.247
Network                 - 192.168.0.248   - 192.168.0.255
```

Pre-built packages exist for many popular Linux distributions and macOS.

Install on Linux (deb based):

```
jemurray@shell:~$ sudo apt install sipcalc
[sudo] password for jemurray:
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  sipcalc
```

Install on macOS (with brew):

```
jemurray@mbp-2019:~ $ brew install sipcalc
==> Downloading https://homebrew.bintray.com/bottles/sipcalc-1.1.6.catalina.bottle.tar.gz
######################################################################## 100.0%
==> Pouring sipcalc-1.1.6.catalina.bottle.tar.gz
🍺  /usr/local/Cellar/sipcalc/1.1.6: 10 files, 63.8KB
```

