---
title: "Removing an IP address from a network interface in macOS"
date: 2020-12-03T09:06:31-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ifconfig
  - macos
  - networking
---

# TL;DR

Remove an IP address from an interface in macOS:

```
jemurray@mbp-2019:~ $ sudo ifconfig en0 delete 172.20.10.4
Password:
```


# Details

A network interface can contain multiple IP address:

```
jemurray@mbp-2019:~ $ ifconfig en0
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether f0:18:98:9b:b5:b2
        inet6 fe80::1cd4:4e32:efbe:c0b9%en0 prefixlen 64 secured scopeid 0x6
        inet6 2600:380:643d:1845:fd:56c8:ea95:642d prefixlen 64 autoconf secured
        inet6 2600:380:643d:1845:2c19:1a54:5f49:b13e prefixlen 64 autoconf temporary
        inet 172.20.10.4 netmask 0xfffffff0 broadcast 172.20.10.15
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect
        status: active
```

To remove a single IP, us the `ifconfig` command with the `delete` option:

```
jemurray@mbp-2019:~ $ sudo ifconfig en0 delete 172.20.10.4
Password:
```

The address is removed:

````
jemurray@mbp-2019:~ $ ifconfig en0
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        options=400<CHANNEL_IO>
        ether f0:18:98:9b:b5:b2
        inet6 fe80::1cd4:4e32:efbe:c0b9%en0 prefixlen 64 secured scopeid 0x6
        inet6 2600:380:643d:1845:fd:56c8:ea95:642d prefixlen 64 autoconf secured
        inet6 2600:380:643d:1845:2c19:1a54:5f49:b13e prefixlen 64 autoconf temporary
        nd6 options=201<PERFORMNUD,DAD>
        media: autoselect
        status: active
```