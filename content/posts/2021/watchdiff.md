---
title: "Repeating the same command while looking for differences"
date: 2021-01-05T12:49:35-06:00
toc: false
images:
categories:
  - tech
tags: 
  - watch
  - linux
  - repeat
  - cli
---

# Overview

The [`watch`](https://gitlab.com/procps-ng/procps) command, in conjunction with the `-d` (display differences) option and a carefully curated input set, will generate a "[`top`](https://gitlab.com/procps-ng/procps) like" interface for realtime display of data sets.  In this example, I am processing [DNS](https://docs.zeek.org/en/current/scripts/base/protocols/dns/main.zeek.html#type-DNS::Info) queries logged by [Zeek](https://zeek.org/).


# Details

This command:

```
watch -d 'cat dns.log | ../../bin/zeek-cut -d query  | sort | uniq -c | sort -rn | head -50'
```
 

Outputs the following information, updated every 2 seconds, with changes highlighted in white:

```
Every 2.0s: cat dns.log | ../../bin/zeek-cut -d query  | sort | uniq -c | sort -rn | head -50                       zeek: Mon Jan  4 18:49:59 2021

    788 www.google.com
    333 google.com
    323 api.smartthings.com
    298 clients4.google.com
    127 play.googleapis.com
    127 e16126.dscg.akamaiedge.net
    109 lh3.googleusercontent.com
     92 gateway.fe.apple-dns.net
     75 www.gstatic.com
     71 cf-st.sc-cdn.net
     60 wirelessdevicestats.googleapis.com
     58 www.youtube.com
     58 i.ytimg.com
     56 proxy.example.com
     54 app-measurement.com
     51 clients3.google.com
     50 wustl.edu
     48 time.google.com
     48 a2047.mm2.akamai.net
     45 206.8.217.172.in-addr.arpa
     43 safebrowsing.googleapis.com
     43 e28622.api5.akamaiedge.net
     42 us-east4-gcp.api.snapchat.com
     42 mesu.apple.com
     42 mesu-cdn.origin-apple.com.akadns.net
```