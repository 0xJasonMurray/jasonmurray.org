---
title: "Using passive DNS to track down CDN traffic"
date: 2020-05-13T12:38:41-05:00
toc: false
images:
categories:
  - tech
tags:
  - passivedns
  - bro
  - zeek
  - nfdump
  - netflow
  - akamai
  - cdn
---

Passive DNS is a historical log of DNS queries and the corresponding answers.  This is helpful when reverse DNS does not return the original DNS query, for example:

Looking up the host `download.windowsupdate.com` returns a number of IP addresses:

```
jemurray@mbp-2019:~ $ host download.windowsupdate.com
download.windowsupdate.com is an alias for 2-01-3cf7-0009.cdx.cedexis.net.
2-01-3cf7-0009.cdx.cedexis.net is an alias for download.windowsupdate.com.edgesuite.net.
download.windowsupdate.com.edgesuite.net is an alias for a767.dspw65.akamai.net.
a767.dspw65.akamai.net has address 23.223.197.56
a767.dspw65.akamai.net has address 23.223.197.10
a767.dspw65.akamai.net has address 23.223.197.24
a767.dspw65.akamai.net has IPv6 address 2600:1407:a000::1730:d118
a767.dspw65.akamai.net has IPv6 address 2600:1407:a000::1730:d113
```

Turning the IP address back into the `download.windowsupdate.com` is the problem.  Instead of returning `download.windowsupdate.com`, an Akamai record is returned:

```
jemurray@mbp-2019:~ $ host 23.223.197.56
56.197.223.23.in-addr.arpa domain name pointer a23-223-197-56.deploy.static.akamaitechnologies.com.
```

Searching through historical DNS query logs for the IP `23.223.197.56` returns the original DNS query, which can be helpful to determine the origin of the request.

```
1589269763.546678	172.21.136.90	50041	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.56,23.223.197.48
```


Here is a real world example where passive DNS helped track down a 10Gbp/s spike in traffic:

![spectrum internet](/images/spectrummax.png)

Netflow reports show the traffic was originating from an Akamai CDN `23.223.197.x`:

```
[jemurray@linux-host flows]$ nfdump -M /var/router/flows -R nfcapd.202005121450:nfcapd.202005121500 -n 40 -s record/bytes
Aggregated flows 3799736
Top 40 flows ordered by bytes:
Date first seen          Event  XEvent Proto      Src IP Addr:Port          Dst IP Addr:Port     X-Src IP Addr:Port        X-Dst IP Addr:Port   In Byte Out Byte
2020-05-12 14:49:34.284 INVALID  Ignore TCP      23.223.197.48:80    ->   192.168.79.225:54093          0.0.0.0:0     ->          0.0.0.0:0        1.6 G        0
2020-05-12 14:55:37.085 INVALID  Ignore TCP      23.223.197.10:80    ->  192.168.139.246:58318          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:55:27.155 INVALID  Ignore TCP      23.223.197.73:80    ->   192.168.79.225:57718          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:55:09.602 INVALID  Ignore TCP      23.223.197.73:80    ->  192.168.174.220:2315           0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:55:10.776 INVALID  Ignore TCP      23.223.197.25:80    ->  192.168.119.109:53724          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:54:58.053 INVALID  Ignore TCP      23.223.197.25:80    ->  192.168.174.220:55578          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:54:22.990 INVALID  Ignore TCP      23.223.197.59:80    ->   192.168.11.235:41688          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:55:12.087 INVALID  Ignore TCP      23.223.197.67:80    ->  192.168.174.220:22827          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:49:07.257 INVALID  Ignore TCP      23.223.197.26:80    ->   192.168.79.225:4656           0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:55:05.291 INVALID  Ignore TCP      23.223.197.25:80    ->   192.168.11.235:56578          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:47:49.283 INVALID  Ignore TCP      23.223.197.56:80    ->  192.168.174.220:12322          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:49:33.322 INVALID  Ignore TCP      23.223.197.16:80    ->   192.168.79.225:30368          0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
2020-05-12 14:49:19.809 INVALID  Ignore TCP      23.223.197.35:80    ->  192.168.174.220:9882           0.0.0.0:0     ->          0.0.0.0:0        1.3 G        0
...
```

A DNS lookup on the IP `23.223.197.48` confirms the owner is Akamai:

```
jemurray@mbp-2019:~ $ host 23.223.197.48
48.197.223.23.in-addr.arpa domain name pointer a23-223-197-48.deploy.static.akamaitechnologies.com.
```

This information does not help to track down what data is being served from Akamai.

We still need to answer the following questions:

- Which Akamai customer is causing the spike in traffic?  
- What was the original request for?  

This is where passive DNS is helpful.  Searching historical DNS query logs in [Zeek](https://zeek.org/) for the Akamai IP address `23.223.197.48` returns the following result: `download.windowsupdate.com.edgesuite.net`.  This confirms the origin of the traffic spike is Microsoft Windows Updates.  

It also happens to be patch Tuesday.

```
[jemurray@linux-host 2020-05-12]$ zcat dns.02:00:00-03:00:00.log.gz | /usr/local/bro/bin/bro-cut ts id.orig_h id.orig_p id.resp_h id.resp_p query answers | egrep '23.223.197.25|23.223.197.26|23.223.197.56'
1589269554.020881	192.168.0.9	30074	208.67.222.222	53	download.windowsupdate.com.edgesuite.net	a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269554.020971	192.168.0.9	30074	208.67.222.222	53	download.windowsupdate.com.edgesuite.net	a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269554.114645	192.168.0.9	24422	208.67.222.222	53	2-01-3cf7-0009.cdx.cedexis.net	download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269554.114761	192.168.0.9	24422	208.67.222.222	53	2-01-3cf7-0009.cdx.cedexis.net	download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269554.200436	192.168.0.9	48707	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269554.200436	192.168.0.9	48707	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269553.976498	172.20.51.104	57842	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269612.682730	192.168.0.17	43547	208.67.220.220	53	2-01-3cf7-0009.cdx.cedexis.net	download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269612.682753	192.168.0.17	43547	208.67.220.220	53	2-01-3cf7-0009.cdx.cedexis.net	download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269612.569496	192.168.0.17	38640	208.67.220.220	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269612.569496	192.168.0.17	38640	208.67.220.220	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269612.683016	192.168.20.177	57540	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269612.716973	172.16.0.105	57540	192.168.0.17	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269662.480917	192.168.0.9	3637	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269662.480917	192.168.0.9	3637	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269662.632843	10.24.48.104	62865	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269662.632843	10.24.48.104	62865	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269662.632843	10.24.48.104	62865	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269763.546678	172.21.136.90	50041	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.56,23.223.197.48
1589269763.386546	192.168.0.9	44265	208.67.220.220	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269763.386546	192.168.0.9	44265	208.67.220.220	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269784.313584	172.17.80.191	55448	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269784.315947	192.168.0.9	61001	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269784.315947	192.168.0.9	61001	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269784.316934	172.17.80.191	60983	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269814.534977	192.168.20.177	64374	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.56,23.223.197.48
1589269814.382347	172.16.0.133	64374	192.168.0.17	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.56,23.223.197.48
1589269814.378991	192.168.0.17	53038	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269814.378991	192.168.0.17	53038	208.67.222.222	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269941.373682	192.168.97.153	18636	184.25.102.71	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269958.418207	172.16.21.72	54083	192.168.0.100	53	download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.56,23.223.197.48
1589269958.455793	192.168.0.25	38702	208.67.220.220	53	2-01-3cf7-0009.cdx.cedexis.net	download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269958.455793	192.168.0.25	38702	208.67.220.220	53	2-01-3cf7-0009.cdx.cedexis.net	download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269958.482206	192.168.0.25	7855	208.67.220.220	53	download.windowsupdate.com.edgesuite.net	a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269958.482254	192.168.0.25	7855	208.67.220.220	53	download.windowsupdate.com.edgesuite.net	a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269958.363987	192.168.0.25	34850	208.67.220.220	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269958.364037	192.168.0.25	34850	208.67.220.220	53	a767.dspw65.akamai.net	23.223.197.48,23.223.197.56
1589269998.872518	192.168.0.17	22779	208.67.220.220	53	www.download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
1589269998.872557	192.168.0.17	22779	208.67.220.220	53	www.download.windowsupdate.com	2-01-3cf7-0009.cdx.cedexis.net,download.windowsupdate.com.edgesuite.net,a767.dspw65.akamai.net,23.223.197.48,23.223.197.56
```
