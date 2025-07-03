---
title: "Using SSH as a proxy to servers on the internal network"
date: 2020-10-03T16:10:46-05:00
toc: false
images:
categories:
  - tech
tags: 
  - ssh
  - proxy
---

# Overview

To access servers on my internal home network from remote locations I use the port-forwarding capabilities of the service provider router (AT&T Router) and an internal SSH jumpbox (Ubuntu Server).  This combination allows me to access any internal systems without the need for a VPN or third-party connection brokerage service.

The network is configured as follows:

![home network port forwarding](/images/homenetportforward.png)


# Details


On the AT&T router, setup port-forwarding:

```
home-server 00:0c:29?49:db:fc 192.168.86.5 22 -> 22 (TCP)
```

I use a dynamic DNS service to keep track of the IP address provided to my home router.  The DNS name `murray-home.ddns.net` is automatically updated if it changes.

On a remote workstation, run the `ssh` command below.  It may look like a normal SSH session, but any connection made to `localhost:2222` will automatically be forwarded to `192.168.86.41:22`:

```
jemurray@mbp-2019:~ $ ssh -L 2222:192.168.86.41:22 murray-home.ddns.net
jemurray@home-server:~$
```

* `-L` - Enable local port forwarding
* `2222` - Listen on the local port 2222
* `192.168.86.41` - When a connection is made to port 22, it is immediately forwarded to: `192.168.86.41 port 22`
* `22` - Connections to port `2222` are forwarded to port `22`


On the **same** host, open a `ssh` session to localhost on port 2222.  The SSH session from the external remote host will be proxied from `localhost:2222` to the internal host `192.168.86.41:22`:


```
jemurray@mbp-2019:~ $ ssh -p 2222 localhost
jemurray@localhost's password:
Last login: Sat Oct  3 17:00:28 2020 from 192.168.86.5
[jemurray@redhat-ansible ~]$
```

Validate the SSH proxy is listening and/or forwarding connections as expected:

```
jemurray@mbp-2019:~ $ netstat -an | grep 2222
# Listening
tcp4       0      0  127.0.0.1.2222         *.*                    LISTEN
tcp6       0      0  ::1.2222               *.*                    LISTEN
# Forwarding
tcp6       0      0  ::1.2222               ::1.63636              ESTABLISHED
tcp6       0      0  ::1.63636              ::1.2222               ESTABLISHED
tcp4       0      0  192.168.86.32.62222    52.202.62.252.443      ESTABLISHED
```

or

```
jemurray@mbp-2019:~ $ lsof -Pn | grep 2222
# Listening
ssh       83906 jemurray    7u     IPv6 0x938978d7b55c7e69         0t0                 TCP [::1]:2222 (LISTEN)
ssh       83906 jemurray    8u     IPv4 0x938978d7911f1139         0t0                 TCP 127.0.0.1:2222 (LISTEN)
# Forwarding
ssh       83906 jemurray   12u     IPv6 0x938978d7a12b9d09         0t0                 TCP [::1]:2222->[::1]:63636 (ESTABLISHED)
ssh       84867 jemurray    4u     IPv6 0x938978d7a12b5fc9         0t0                 TCP [::1]:63636->[::1]:2222 (ESTABLISHED)
ssh       84867 jemurray    6u     IPv6 0x938978d7a12b5fc9         0t0                 TCP [::1]:63636->[::1]:2222 (ESTABLISHED)
```