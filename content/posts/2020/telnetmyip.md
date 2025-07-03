---
title: "Using telnet to find a devices current IP address"
date: 2020-04-22T12:22:57-05:00
categories:
  - tech
tags: [til, telnet, ip]
---


Using `telnet` to find a devices current IP address:

```
jemurray@mbp-2019:~ $ telnet telnetmyip.com
Trying 2600:1f16:b9f:7d16::100...
Connected to telnetmyip.com.
Escape character is '^]'.
{


"comment": "##     Your IP Address is 2600:6c40:7e80:2515:e4c0:4484:93e6:a62b (62588)     ##",


"family": "ipv6",
"ip": "2600:6c40:7e80:2515:e4c0:4484:93e6:a62b",
"port": "62588",
"protocol": "telnet",
"version": "v1.2.0",
"website": "https://github.com/packetsar/checkmyip",
"sponsor": "Sponsored by ConvergeOne, https://www.convergeone.com/"
}
Connection closed by foreign host.
```
