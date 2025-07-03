---
title: "Network path tracing commands"
date: 2020-05-12T07:30:52-05:00
toc: false
images:
categories:
  - tech
tags:
  - networking
  - traceroute
  - troubleshooting
---


## Description

The Internet is a large and complex aggregation of network hardware, connected by gateways. Tracking the route one’s packets follow (or finding the miscreant gateway that’s discarding your packets) can be difficult.  traceroute use the IP protocol ‘time to live’ field and attempts to elicit an ICMP TIME_EXCEEDED response from each gateway along the path to some host.  (Source: [BSD man page](https://www.freebsd.org/cgi/man.cgi?query=traceroute&apropos=0&sektion=0&manpath=FreeBSD+12.1-RELEASE+and+Ports&arch=default&format=html))

Here are several variations, and examples of the traceroute command.

## Linux traceroute

Standard traceroute command

```
[jemurray@linux-host ~]$ traceroute bost-pt1.es.net
traceroute to bost-pt1.es.net (198.124.238.66), 30 hops max, 60 byte packets
 1  eth4-24-eps-core.nts.wustl.edu (128.252.5.114)  0.645 ms  0.694 ms  0.769 ms
 2  xe-0-0-1-bih-1017-wu-rt-0.nts.wustl.edu (128.252.1.62)  0.345 ms xe-0-0-1-eps-l29-wu-rt-0.nts.wustl.edu (128.252.1.42)  0.298 ms  0.314 ms
 3  xe-1-2-1-900w-mmr-wu-rt-0.net.wustl.edu (128.252.1.252)  0.850 ms  0.867 ms  0.861 ms
 4  xe-0-0-5.944.rtr.ll.indiana.gigapop.net (149.165.254.141)  10.521 ms  10.517 ms  10.524 ms
 5  149.165.254.22 (149.165.254.22)  14.219 ms  14.230 ms  14.223 ms
 6  washcr5-ip-a-chiccr5.es.net (134.55.36.46)  31.446 ms  31.501 ms  31.818 ms
 7  * * *
 8  newycr5-ip-a-aofacr5.es.net (134.55.37.78)  36.763 ms  36.834 ms  37.125 ms
 9  bostcr5-ip-a-newycr5.es.net (134.55.209.33)  41.370 ms  41.402 ms  41.300 ms
10  bost-pt1.es.net (198.124.238.66)  41.072 ms  41.094 ms  41.104 ms
```

## Linux paris-traceroute

traceroute that responds to load balancing routers, allows a user to distinguish between the presence of per-flow load balancing and per-packet load balancing.

```
[jemurray@linux-host ~]$ sudo paris-traceroute bost-pt1.es.net
traceroute to bost-pt1.es.net (198.124.238.66), 30 hops max, 30 bytes packets
 1  eth4-24-eps-core.nts.wustl.edu (128.252.5.114)  0.673ms    0.699ms    0.804ms
 2  xe-0-0-1-eps-l29-wu-rt-0.nts.wustl.edu (128.252.1.42)  0.323ms    0.321ms    0.326ms
 3  xe-0-0-1-900w-mmr-wu-rt-0.net.wustl.edu (128.252.100.126)  0.998ms    0.996ms    1.000ms
 4  xe-0-0-5.944.rtr.ll.indiana.gigapop.net (149.165.254.141)  10.703ms    10.709ms    10.729ms
 5  149.165.254.22 (149.165.254.22)  14.245ms    14.252ms    14.253ms
 6  washcr5-ip-a-chiccr5.es.net (134.55.36.46)  31.527ms    31.777ms    32.075ms
 7  * * *
 8  newycr5-ip-a-aofacr5.es.net (134.55.37.78)  36.929ms    37.030ms    37.222ms
 9  bostcr5-ip-a-newycr5.es.net (134.55.209.33)  41.386ms    41.515ms    41.773ms
10  bost-pt1.es.net (198.124.238.66)  41.218ms    41.221ms    41.229ms
```

## Linux tracepath

Attempts to discover MTU along the path.

```
[jemurray@linux-host ~]$ tracepath bost-pt1.es.net
 1?: [LOCALHOST]                                         pmtu 9000
 1:  eth4-24-eps-core.nts.wustl.edu                        0.850ms
 1:  eth4-24-eps-core.nts.wustl.edu                        0.800ms
 2:  xe-0-0-1-eps-l29-wu-rt-0.nts.wustl.edu                1.169ms asymm  3
 3:  xe-0-0-1-900w-mmr-wu-rt-0.net.wustl.edu               1.845ms asymm  4
 4:  xe-0-0-5.944.rtr.ll.indiana.gigapop.net              11.103ms asymm  5
 5:  no reply
 6:  washcr5-ip-a-chiccr5.es.net                          31.739ms asymm  7
 7:  no reply
 8:  newycr5-ip-a-aofacr5.es.net                          36.985ms asymm  9
 9:  bostcr5-ip-a-newycr5.es.net                          41.593ms asymm 10
10:  bost-pt1.es.net                                      41.364ms !H
     Resume: pmtu 9000
```

## Linux MTR

Combines continuous traceroute and ping together in a single command (report mode run in the example below)

```
[jemurray@linux-host ~]$ mtr --report bost-pt1.es.net
Start: Tue May 12 07:34:18 2020
HOST: linux-host.accounts.ad.wu Loss%   Snt   Last   Avg  Best  Wrst StDev
  1.|-- eth4-24-eps-core.nts.wust  0.0%    10    0.9   0.8   0.7   1.0   0.0
  2.|-- xe-0-0-1-bih-1017-wu-rt-0  0.0%    10    0.6   0.5   0.3   0.6   0.0
  3.|-- xe-1-2-1-900w-mmr-wu-rt-0  0.0%    10    1.0   1.0   0.9   1.1   0.0
  4.|-- xe-0-0-5.944.rtr.ll.india  0.0%    10   12.0  11.0  10.6  12.0   0.3
  5.|-- 149.165.254.22             0.0%    10   14.4  14.3  14.2  14.4   0.0
  6.|-- washcr5-ip-a-chiccr5.es.n  0.0%    10   31.6  31.7  31.6  32.2   0.0
  7.|-- ???                       100.0    10    0.0   0.0   0.0   0.0   0.0
  8.|-- newycr5-ip-a-aofacr5.es.n  0.0%    10   37.0  37.0  36.9  37.2   0.0
  9.|-- bostcr5-ip-a-newycr5.es.n  0.0%    10   41.4  41.6  41.4  41.9   0.0
 10.|-- bost-pt1.es.net            0.0%    10   41.2  41.2  41.2  41.3   0.0
```

## Reverse tracepath using PerSonar pscheduler

The perfSonar toolset can be used to run a number of network telemetry commands on a remote host, in this example tracepath is used to determine the reverse path.

```
[jemurray@linux-host ~]$ pscheduler task --tool tracepath trace --source 128.252.41.194 --dest 128.252.5.113
Submitting task...
Task URL:
https://128.252.41.194/pscheduler/tasks/9d51bcb9-0125-41fe-bf5d-c1c185283a81
Running with tool 'tracepath'
Fetching first run...

Next scheduled run:
https://128.252.41.194/pscheduler/tasks/9d51bcb9-0125-41fe-bf5d-c1c185283a81/runs/7d448628-3b4d-400e-8445-39cab6a79457
Starts 2020-05-12T12:46:36Z (~3 seconds)
Ends   2020-05-12T12:48:17Z (~100 seconds)
Waiting for result...

1	xe-0-0-8-900w-mmr-wu-rt-0.net.wustl.edu (128.252.41.193) AS2552 1.142 ms mtu 9000 bytes
	  WUSTL-ASN, US
2	xe-0-0-5-bih-1017-wu-rt-0.nts.wustl.edu (128.252.1.253) AS2552 1.738 ms mtu 9000 bytes
	  WUSTL-ASN, US
3	eth4-14-med-core.nts.wustl.edu (128.252.1.123) AS2552 1.553 ms mtu 9000 bytes
	  WUSTL-ASN, US
4	eth7-4-eps-core.nts.wustl.edu (128.252.100.33) AS2552 1.835 ms mtu 9000 bytes
	  WUSTL-ASN, US
5	ps-eps-l29-1.wustl.edu (128.252.5.113) AS2552 0.983 ms mtu 9000 bytes host-unreachable
	  WUSTL-ASN, US

No further runs scheduled.
```

### Cisco MPLS traceroute

Traceroute command on Cisco device to trace the MPLS path.

```
he-router#traceroute mpls ipv4 128.252.247.156 255.255.255.255
Tracing MPLS Label Switched Path to 128.252.247.156/32, timeout is 2 seconds

Codes: '!' - success, 'Q' - request not sent, '.' - timeout,
  'L' - labeled output interface, 'B' - unlabeled output interface,
  'D' - DS Map mismatch, 'F' - no FEC mapping, 'f' - FEC mismatch,
  'M' - malformed request, 'm' - unsupported tlvs, 'N' - no label entry,
  'P' - no rx intf label prot, 'p' - premature termination of LSP,
  'R' - transit router, 'I' - unknown upstream index,
  'X' - unknown return code, 'x' - return code 0

Type escape sequence to abort.
  0 128.252.161.109 MRU 9154 [Labels: 23 Exp: 0]
L 1 128.252.161.108 MRU 9154 [Labels: 337200 Exp: 0] 0 ms
L 2 128.252.247.151 MRU 9168 [Labels: implicit-null Exp: 0] 20 ms
! 3 128.252.161.27 1 ms
```

## JunOS traceroute with AS lookup

Traceroute on JunOS with AS path lookup

```
jemurray@eps-l29-wu-rt-0> traceroute as-number-lookup 198.124.238.66
traceroute to 198.124.238.66 (198.124.238.66), 30 hops max, 52 byte packets
 1  xe-0-0-1-900w-mmr-wu-rt-0.net.wustl.edu (128.252.100.126)  1.198 ms  1.172 ms  1.092 ms
 2  xe-0-0-5.944.rtr.ll.indiana.gigapop.net (149.165.254.141) [AS  19782]  11.212 ms  10.768 ms  10.860 ms
 3  149.165.254.22 (149.165.254.22) [AS  19782]  14.416 ms  14.617 ms  14.419 ms
 4  washcr5-ip-a-chiccr5.es.net (134.55.36.46) [AS  293]  33.056 ms  31.736 ms  31.788 ms
 5  * * *
 6  newycr5-ip-a-aofacr5.es.net (134.55.37.78) [AS  293]  37.315 ms  37.048 ms  37.000 ms
 7  bostcr5-ip-a-newycr5.es.net (134.55.209.33) [AS  293]  41.692 ms  43.416 ms  43.097 ms
 8  bost-pt1.es.net (198.124.238.66) [AS  291]  42.084 ms  41.361 ms  41.404 ms

{master}
```

## Windows tracert

Standard traceroute command on Microsoft Windows

```
PS C:\Users\Jason Murray> tracert 128.252.5.113

Tracing route to ps-eps-l29-1.wustl.edu [128.252.5.113]
over a maximum of 30 hops:

 1    <1 ms    <1 ms    <1 ms  testwifi.here [192.168.86.1]
 2     *        *        *     Request timed out.
 3    11 ms    10 ms    10 ms  096-034-051-080.biz.spectrum.com [96.34.51.80]
 4    11 ms    10 ms    11 ms  crr01olvemo-bue-40.olve.mo.charter.com [96.34.76.160]
 5    10 ms     9 ms    11 ms  crr02ovldmo-bue-200.ovld.mo.charter.com [96.34.76.136]
 6    12 ms    10 ms    11 ms  dtr02ovldmo-tge-0-7-0-0.ovld.mo.charter.com [96.34.48.205]
 7     9 ms    11 ms    10 ms  150.181.1.18
 8    16 ms    18 ms    12 ms  035-130-036-042.biz.spectrum.com [35.130.36.42]
 9    10 ms    20 ms    10 ms  xe-7-2-0-bih-1017-wu-rt-0.net.wustl.edu [128.252.182.131]
10    15 ms    13 ms    10 ms  eth4-14-med-core.nts.wustl.edu [128.252.1.123]
11    10 ms    11 ms    11 ms  eth7-4-eps-core.nts.wustl.edu [128.252.100.33]
12    10 ms    11 ms    17 ms  linux-host.accounts.ad.wustl.edu [128.252.5.113]

Trace complete.
```

## perfSonar web reverse-path tool

```
http://128.252.5.113/toolkit/gui/reverse_traceroute.cgi?choice=yes

Executing exec(traceroute -m 30 -q 3 47.24.232.181 140)
traceroute to 47.24.232.181 (47.24.232.181), 30 hops max, 140 byte packets
 1  eth4-24-eps-core.nts.wustl.edu (128.252.5.114)  1.312 ms  1.330 ms  1.386 ms
 2  xe-0-0-1-eps-l29-wu-rt-0.nts.wustl.edu (128.252.1.42)  0.259 ms  0.333 ms
    xe-0-0-1-bih-1017-wu-rt-0.nts.wustl.edu (128.252.1.62)  1.276 ms
 3  xe-0-0-0-sgl-001b-wu-rt-0.net.wustl.edu (128.252.182.128)  0.322 ms
    xe-0-0-1-sgl-001b-wu-rt-0.net.wustl.edu (128.252.182.130)  0.381 ms
    xe-0-0-0-sgl-001b-wu-rt-0.net.wustl.edu (128.252.182.128)  0.325 ms
 4  035-130-036-041.biz.spectrum.com (35.130.36.41)  0.514 ms  0.535 ms  0.447 ms
 5  150.181.1.19 (150.181.1.19)  1.220 ms  1.337 ms  1.460 ms
 6  crr02ovldmo-tge-0-7-0-8.ovld.mo.charter.com (96.34.48.204)  0.759 ms  1.052 ms  1.041 ms
 7  dtr01stprmo-bue-30.stpr.mo.charter.com (96.34.76.143)  1.745 ms  1.756 ms  1.679 ms

0traceroute -m 30 -q 3 47.24.232.181 140 took 25secs. Total script traceroute.pl time=25secs, user=apache@ps-eps-l29-1.wustl.edu, OS=linux
```
