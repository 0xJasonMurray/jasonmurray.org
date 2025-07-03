---
title: "Google WiFi drops DNS queries that return a RFC1918 answer"
date: 2020-11-12T14:30:58-06:00
toc: false
images:
categories:
  - tech
tags: 
  - googlewifi
  - dns
  - rfc1918
---

# Overview

Google WiFi routers don't allow DNS queries that return RFC1918 answers in the response:

```
jemurray@mbp-2019:~ $ dig @192.168.86.1 home-server.jasonmurray.org

; <<>> DiG 9.10.6 <<>> @192.168.86.1 home-server.jasonmurray.org
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 33369
;; flags: qr; QUERY: 0, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 0

;; Query time: 65 msec
;; SERVER: 192.168.86.1#53(192.168.86.1)
;; WHEN: Thu Nov 12 14:43:25 CST 2020
;; MSG SIZE  rcvd: 12
```

These queries are blocked to mitigate potential DNS rebinding attacks.


Repeating the same query to Google's public DNS servers works without issue:

```
jemurray@mbp-2019:~ $ dig @8.8.8.8 home-server.jasonmurray.org

; <<>> DiG 9.10.6 <<>> @8.8.8.8 home-server.jasonmurray.org
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44874
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;home-server.jasonmurray.org.	IN	A

;; ANSWER SECTION:
home-server.jasonmurray.org. 299 IN	A	192.168.86.5

;; Query time: 147 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Nov 12 14:48:37 CST 2020
;; MSG SIZE  rcvd: 72
```


# Details

[Google Wifi](https://store.google.com/us/product/google_wifi) supplies network connectivity for wired and wireless connections at my house.  The setup looks like this:

![](/images/2020-11-12-14-36-37.png)

I use the domain `jasonmurray.org` to host DNS entries for both public and private network connected devices.  For example my public web server `jasonmurray.org` is `104.18.60.146` and one of my internal servers `home-server.jasonmurray.org` is `192.168.86.5`.  I don't feel the need to setup an internal DNS server or care about leaking RFC1918 DNS entries to the world.  This setup works fine for me.

However, the DNS server built into the Google WiFi router won't allow DNS queries that return RFC1918 space.  The DNS server returns a `REFUSED` status code.   

```
jemurray@mbp-2019:~ $ dig @192.168.86.1 home-server.jasonmurray.org

; <<>> DiG 9.10.6 <<>> @192.168.86.1 home-server.jasonmurray.org
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 33369
;; flags: qr; QUERY: 0, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 0

;; Query time: 65 msec
;; SERVER: 192.168.86.1#53(192.168.86.1)
;; WHEN: Thu Nov 12 14:43:25 CST 2020
;; MSG SIZE  rcvd: 12
```

After a bit of debugging with `tcpdump`.   I tracked down the problem to this Google document: https://support.google.com/wifi/answer/9144137?hl=en - Google blocks DNS queries that return RFC1918 because of a potential [DNS rebinding](https://support.google.com/googlenest/answer/9144137#dns_rebinding_attack) attack.

This was perplexing because I would see the DNS query leave the laptops public network interface:

```
14:55:01.077839 IP 192.168.86.210.52571 > 192.168.86.1.53: 33057+ [1au] A? home-server.jasonmurray.org. (56)
```

The query then egressed the ATT router, and returned with a successful answer:

```
20:55:01.087423 IP 99.28.31.196.10750 > 8.8.8.8.53: 60894+ [1au] A? home-server.jasonmurray.org. (56)
20:55:01.121433 IP 8.8.8.8.53 > 99.28.31.196.10750: 60894$ 1/0/1 A 192.168.86.5 (72)
```

Until the response returned to my laptop:

```
14:55:01.125153 IP 192.168.86.1.53 > 192.168.86.210.52571: 33057 Refused- [0q] 0/0/0 (12)
```

This indicated that somewhere between the home router and my laptop the query response was failing.

A quick Google for: `google wifi router won't return rfc1918 dns queries` - helped me to confirm that my router was indeed the cause of the issue.

