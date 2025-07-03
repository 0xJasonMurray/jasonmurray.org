---
title: "Determine the active OpenDNS anycast server"
date: 2020-11-10T15:37:22-06:00
toc: false
images:
categories:
  - tech
tags: 
  - anycast
  - opendns
---

In anycast, a single IP address can be configured on many servers.  Anycast is used to deploy highly available and geographically redundant services.  For example, OpenDNS uses an anycast network to deploy DNS servers [all over the world](https://www.opendns.com/data-center-locations/).    Routing "tricks" takes care of sending traffic to the closest geographic server.

If servers are deployed around the world and use the same address, how can we determine which server is processing the requests?  A diagnostic mechanism must be built into the application.   In the case of OpenDNS, a TXT DNS query is made to `debug.opendns.com`, which returns the location and various other debugging information.

```
jemurray@mbp-2019:~ $ dig @208.67.220.220 debug.opendns.com TXT

; <<>> DiG 9.10.6 <<>> @208.67.220.220 debug.opendns.com TXT
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 2826
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;debug.opendns.com.		IN	TXT

;; ANSWER SECTION:
debug.opendns.com.	0	IN	TXT	"server r6.chi"
debug.opendns.com.	0	IN	TXT	"flags 40020 0 70 180000000000000000007950800000000000000"
debug.opendns.com.	0	IN	TXT	"originid 0"
debug.opendns.com.	0	IN	TXT	"actype 0"
debug.opendns.com.	0	IN	TXT	"source 99.28.31.196:58574"

;; Query time: 48 msec
;; SERVER: 208.67.220.220#53(208.67.220.220)
;; WHEN: Tue Nov 10 14:42:19 CST 2020
;; MSG SIZE  rcvd: 223
```