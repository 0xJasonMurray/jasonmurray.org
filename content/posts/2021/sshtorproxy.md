---
title: "Connecting to a Tor onion domain over SSH"
date: 2021-02-01T06:00:59-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tor
  - ssh
  - proxy
---

# Overview

In order to connect to a `.onion` URL or domain through SSH, the connection must be relayed through a Tor SOCKS proxy with the `-o ProxyCommand` option:

```
ssh -o ProxyCommand='nc -x 127.0.0.1:9050 %h %p' trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion
```

# Details

First, start the `tor` service on the system running the `ssh` client:

```
jemurray@Jasons-MacBook-Pro:~ $ tor
Jan 30 16:03:59.884 [notice] Tor 0.4.4.6 running on Darwin with Libevent 2.1.12-stable, OpenSSL 1.1.1i, Zlib 1.2.11, Liblzma N/A, and Libzstd N/A.
Jan 30 16:03:59.884 [notice] Tor can't help you if you use it wrong! Learn how to be safe at https://www.torproject.org/download/download#warning
Jan 30 16:03:59.884 [notice] Configuration file "/usr/local/etc/tor/torrc" not present, using reasonable defaults.
Jan 30 16:03:59.886 [notice] Opening Socks listener on 127.0.0.1:9050
Jan 30 16:03:59.886 [notice] Opened Socks listener on 127.0.0.1:9050
Jan 30 16:03:59.000 [notice] Parsing GEOIP IPv4 file /usr/local/Cellar/tor/0.4.4.6/share/tor/geoip.
Jan 30 16:04:00.000 [notice] Parsing GEOIP IPv6 file /usr/local/Cellar/tor/0.4.4.6/share/tor/geoip6.
Jan 30 16:04:00.000 [notice] Bootstrapped 0% (starting): Starting
Jan 30 16:04:00.000 [notice] Starting with guard context "default"
Jan 30 16:04:01.000 [notice] Bootstrapped 5% (conn): Connecting to a relay
Jan 30 16:04:01.000 [notice] Bootstrapped 10% (conn_done): Connected to a relay
Jan 30 16:04:01.000 [notice] Bootstrapped 14% (handshake): Handshaking with a relay
Jan 30 16:04:01.000 [notice] Bootstrapped 15% (handshake_done): Handshake with a relay done
Jan 30 16:04:01.000 [notice] Bootstrapped 75% (enough_dirinfo): Loaded enough directory info to build circuits
Jan 30 16:04:01.000 [notice] Bootstrapped 90% (ap_handshake_done): Handshake finished with a relay to build circuits
Jan 30 16:04:01.000 [notice] Bootstrapped 95% (circuit_create): Establishing a Tor circuit
Jan 30 16:04:02.000 [notice] Bootstrapped 100% (done): Done
```

Establish an `ssh` session by using the `-o ProxyCommand` and `netcat (nc)`:


```
jemurray@Jasons-MacBook-Pro:~ $ ssh -o ProxyCommand='nc -x 127.0.0.1:9050 %h %p' trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion
Autopushing login request to phone...
Success. Logging you in...
Linux shell 4.19.0-6-cloud-amd64 #1 SMP Debian 4.19.67-2+deb10u1 (2019-09-20) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

jemurray@shell:~$
```