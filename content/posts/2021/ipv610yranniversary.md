---
title: "Ten Year Anniversary of IPv6 at WashU"
date: 2021-05-26T07:53:43-05:00
toc: false
images:
categories:
  - tech
tags: 
  - ipv6
  - networking
  - washu
description: Ten years ago today (May 26, 2011), WashU enabled IPv6 on the edge, core, and DNS infrastructure.
---
Ten years ago today (May 26, 2011), I enabled IPv6 on the [WashU](https://wustl.edu/) edge, core, and DNS infrastructure.

Here is the first IPv6 DNS packet to traverse the IPv6 stack (I know, it does not return an IPv6 answer, we had to start somewhere...):

```
jemurray@phalanges:~/ipv6 $ cat first-dns-query.txt
May 26 2011 7:50AM CDT


unicast
============
jemurray@kimber:~ $ dig @2604:3e00:0:4::1 wustl.edu

; <<>> DiG 9.6.0-APPLE-P2 <<>> @2604:3e00:0:4::1 wustl.edu
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 10359
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;wustl.edu.			IN	A

;; ANSWER SECTION:
wustl.edu.		3600	IN	A	128.252.73.216

;; Query time: 21 msec
;; SERVER: 2604:3e00:0:4::1#53(2604:3e00:0:4::1)
;; WHEN: Thu May 26 07:36:28 2011
;; MSG SIZE  rcvd: 43




Anycast
============

jemurray@kimber:~ $ dig @2604:3e00::1 wustl.edu

; <<>> DiG 9.6.0-APPLE-P2 <<>> @2604:3e00::1 wustl.edu
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 62345
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;wustl.edu.			IN	A

;; ANSWER SECTION:
wustl.edu.		3600	IN	A	128.252.73.216

;; Query time: 18 msec
;; SERVER: 2604:3e00::1#53(2604:3e00::1)
;; WHEN: Thu May 26 07:53:43 2011
;; MSG SIZE  rcvd: 43
```