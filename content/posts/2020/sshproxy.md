---
title: "Setting up an OpenSSH bastion host for an entire DNS domain"
date: 2020-11-04T16:36:19-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ssh
  - proxy
  - bastion
---

# Overview

Remote administration via `ssh` is a standard practice for many unix based servers or networking devices.  Protecting this critical infrastructure by provisioning internal devices on `RFC1918` IP space and/or limiting inbound connections to specific `bastion` or `jump-hosts` is a common method to limit external exposure.

Bastion hosts are security hardened devices who's sole purpose is to act as a gateway into the production environment.  These servers should be stripped down to essential services and patched on a regular basis. The server typically sits between the untrusted and trusted network, as shown in the diagram below: 

![SSH proxy flow](/images/ssh-proxy.png)

To make the bastion host configuration easier, I prefer to provision all systems on an internal DNS domain name.  [OpenSSH](https://www.openssh.com/) is configured through the `Host` and `ProxyCommand` options to key off the wildcard domain, and force all devices through the bastion host.


# Details


Configure OpenSSH to proxy any device in the domain `*.internal.example.com` through `bastion.example.com` by adding the following lines to the `~/.ssh/config` for individual users or globally by editing `/etc/ssh/ssh_config`:

```
jemurray@mbp-2019:~ $ cat .ssh/config
Host *.internal.example.com
        ProxyCommand ssh bastion.example.com -W %h:%p
```

Any `ssh` connection made to `*.internal.example.com` will be automatically proxied through the bastion host:

```
jemurray@mbp-2019:~ $ ssh router.internal.example.com
***********************************************************************
*        WARNING - Unauthorized access to or use of this system is    *
*                         strictly prohibited.                        *
***********************************************************************

jemurray@router.internal.example.com's password:
Cisco Nexus Operating System (NX-OS) Software
TAC support: http://www.cisco.com/tac
The copyrights to certain works contained in this software are
owned by other third parties and used and distributed under
license. Certain components of this software are licensed under
the GNU General Public License (GPL) version 2.0 or the GNU
Lesser General Public License (LGPL) Version 2.1. A copy of each
such license is available at
router#
```

