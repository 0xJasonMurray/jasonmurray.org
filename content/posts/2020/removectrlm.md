---
title: "Using vi to remove ^M characters in a text file"
date: 2020-07-30T15:09:23-05:00
toc: false
images:
categories:
  - tech
tags: 
  - return
  - newline
  - vi
---

A text file that contains `^M` characters was probably created on a DOS or Windows system and then opened on a Unix system.   `^M` is the DOS / Windows character for carriage-return, otherwise known as the end of a line in the text file.

To remove the characters, open the text file in `vi`.  While in command mode type:

```
:% s/^M//g
```

Note: You may have to type: `<ctrl-v><ctrl-m>` to create the `^M` character.  It is not a `^` and `M`.

Example file with `^M` characters throughout:

```
! Info via SNMP: sysoid=1.3.6.1.4.1.2636.1.1.1.2.25,vendor=Juniper,model=MX480,hostname=900w-mmr-wu-rt-0^M
## Last commit: 2020-06-17 05:04:35 CDT by me ^M
system {^M
    host-name 900w-mmr-wu-rt-0;^M
    time-zone America/Chicago;^M
    no-redirects;^M
    no-redirects-ipv6;^M
    dump-on-panic;^M
    internet-options {^M
        /* KB28184 tells us that the Junos default is 1000 ICMP messages per second */^M
        icmpv4-rate-limit packet-rate 1000;^M
        icmpv6-rate-limit packet-rate 1000;^M
        path-mtu-discovery;^M
        /* Routers shouldn’t process source quench messages (RFC1812) and RFC6633 formally deprecates its handing in other transport protocols. */^M
        no-source-quench;^M
```

