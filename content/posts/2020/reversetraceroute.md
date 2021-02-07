---
title: "Reverse traceroute with PerfSonar"
date: 2020-08-18T10:19:48-05:00
toc: false
images:
categories:
  - tech
tags: 
  - perfsonar
  - pscheduler
  - tracepath
---

What goes out must come in, this is true for two way communication on the Internet.   An outbound path may not always be the same for return traffic.  One way to validate asymmetric routing is to run a `traceroute` from source to destination and then from destination to source.  This is made difficult because most people do not have access to a system at the destination to initiate the `traceroute` from.  

The PerfSonar project can help.  There is the`tracepath` tool, when called through the `pScheduler` command both the outbound and inbound network paths can be mapped.

In this example, we are troubleshooting an asymmetric routing problem with the Internet2 provider.  The outbound traffic is being properly routed, however inbound is returning through the commodity providers.  

By using the `tracepath` tool the problem becomes obvious.  Return traffic is coming back over the Charter link, not the Indiana GigaPOP (Internet2) link as it should be.  

```
[jemurray@wuit-s-00050 ~]$ pscheduler task --tool tracepath trace --source bost-pt1.es.net --dest 128.252.41.194
Submitting task...
Task URL:
https://bost-pt1.es.net/pscheduler/tasks/0cef0927-e231-4989-8bb0-d2bee6b88bc8
Running with tool 'tracepath'
Fetching first run...

Next scheduled run:
https://bost-pt1.es.net/pscheduler/tasks/0cef0927-e231-4989-8bb0-d2bee6b88bc8/runs/fb3430f5-3405-4922-b230-bce46477ef54
Starts 2020-08-18T07:12:51-07 (~2 seconds)
Ends   2020-08-18T07:14:32-07 (~100 seconds)
Waiting for result...

1	bostcr1-bostpt1.es.net (198.124.238.65) AS291 0.415 ms mtu 9000 bytes
	  ESNET-EAST, US
2	newycr5-ip-a-bostcr5.es.net (134.55.209.34) AS293 4.925 ms mtu 9000 bytes
	  ESNET, US
3	No Response
4	washcr5-ip-a-aofacr5.es.net (134.55.36.34) AS293 10.153 ms mtu 9000 bytes
	  ESNET, US
5	eqxashcr5-ip-c-washcr5.es.net (134.55.219.2) AS293 10.541 ms mtu 9000 bytes
	  ESNET, US
6	eqix-dc5.chartercom.com (206.126.236.115) 11.017 ms mtu 1500 bytes
7	bbr02ashbva-bue-6.ashb.va.charter.com (96.34.3.88) 46.317 ms mtu 1500 bytes
8	bbr01sgnwmi-tge-0-2-0-8.sgnw.mi.charter.com (96.34.0.245) 46.747 ms mtu 1500 bytes
9	crr02blvlil-bue-110.blvl.il.charter.com (96.34.2.171) 39.787 ms mtu 1500 bytes
10	crr01ovldmo-bue-200.ovld.mo.charter.com (96.34.76.130) 40.994 ms mtu 1500 bytes
11	dtr01ovldmo-bue-100.ovld.mo.charter.com (96.34.49.221) 40.932 ms mtu 1500 bytes
12	150.181.1.16 39.591 ms mtu 1500 bytes
13	No Response
14	xe-7-2-0-eps-l29-wu-rt-0.net.wustl.edu (128.252.182.129) AS2552 39.882 ms mtu 1500 bytes
	  WUSTL-ASN, US
15	xe-0-0-1-900w-mmr-wu-rt-0.net.wustl.edu (128.252.100.126) AS2552 39.932 ms mtu 1500 bytes
	  WUSTL-ASN, US
16	wuit-s-00050.accounts.ad.wustl.edu (128.252.41.194) AS2552 39.809 ms mtu 1500 bytes host-unreachable
	  WUSTL-ASN, US

No further runs scheduled.
```


After some traffic engineering, the problem is resolved and tested by the `tracepath` tool again.  This time the return path flows through the Internet2 link:

```
[jemurray@wuit-s-00050 ~]$ pscheduler task --tool tracepath trace --source bost-pt1.es.net --dest 128.252.41.194
Submitting task...
Task URL:
https://bost-pt1.es.net/pscheduler/tasks/503a0403-241c-493c-a182-d6955f9c1f99
Running with tool 'tracepath'
Fetching first run...

Next scheduled run:
https://bost-pt1.es.net/pscheduler/tasks/503a0403-241c-493c-a182-d6955f9c1f99/runs/bd8d48b3-666b-49d3-aa30-5f4ffb3ea5e3
Starts 2020-08-18T08:22:47-07 (~2 seconds)
Ends   2020-08-18T08:24:28-07 (~100 seconds)
Waiting for result...

1	bostcr1-bostpt1.es.net (198.124.238.65) AS291 0.495 ms mtu 9000 bytes
	  ESNET-EAST, US
2	newycr5-ip-a-bostcr5.es.net (134.55.209.34) AS293 4.956 ms mtu 9000 bytes
	  ESNET, US
3	No Response
4	washcr5-ip-a-aofacr5.es.net (134.55.36.34) AS293 10.117 ms mtu 9000 bytes
	  ESNET, US
5	eqxashcr5-ip-c-washcr5.es.net (134.55.219.2) AS293 10.511 ms mtu 9000 bytes
	  ESNET, US
6	eqxchicr5-ip-a-eqxashcr5.es.net (134.55.207.54) AS293 25.051 ms mtu 9000 bytes
	  ESNET, US
7	xe-1-2-0.944-900w-mmr-wu-rt-0.net.wustl.edu (149.165.254.142) AS19782 33.239 ms mtu 9000 bytes
	  INDIANAGIGAPOP, US
8	wuit-s-00050.accounts.ad.wustl.edu (128.252.41.194) AS2552 30.501 ms mtu 9000 bytes host-unreachable
	  WUSTL-ASN, US

No further runs scheduled.
```

An added benefit of the `tracepath` tool is hop-by-hop MTU detection.  Also helpful when troubleshooting high-speed data transfer problems.  