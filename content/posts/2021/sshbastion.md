---
title: "Using a bastion host with SSH"
date: 2021-03-02T10:19:52-06:00
toc: false
images:
categories:
  - tech
tags: 
  - bastion
  - jumphost
  - ssh
  - proxy
description: An ssh bastion host is a server that sits between the secure inside network and the insecure outside network.  Connections to a host on the inside must be proxied through the bastion host, direct access is not allowed.  Bastion hosts reduce the attack surface by only exposing a single host to the untrusted network.
---

## Key Points

- Bastion hosts limit the attack surface of critical devices.
- Use the `-J` option with `ssh` to automatically proxy connections through a bastion host: `ssh -A -J bastion-host-1.example.org host-3.example.org`.
- Multiple host can be specified in the `-J` option: `ssh -A -J bastion-host-1.example.org,bastion-host-2.example.org host-3.example.org`

## Overview

An `ssh` bastion host is a server that sits between the secure `inside` network and the insecure `outside` network.  Connections to a host on the inside must be proxied through the bastion host, direct access is not allowed.  Bastion hosts reduce the attack surface by only exposing a single host to the untrusted network. In addition, bastion hosts are hardened devices, closely monitored, regularly patched, and limit services to `ssh` only.

There are a variety of ways to configure the bastion host environment. In this example, we will assume all systems are directly connected to the public Internet, but their host-based-firewalls block all communication except for `ssh` from the bastion-host:

[![Image of ](/images/2021-03-02-10-46-53.png)](/images/2021-03-02-10-46-53.png)

## Details

### The Old Way

Prior to the `-J` option, connecting through a bastion host was a manual operation.  A user first connects to the bastion host, then creates another connection to the final destination.  Similar to this:

```
jemurray@jasons-mbp:~ $ ssh -A bastion-host-1.example.org
jemurray@bastion-host-1:~$

jemurray@bastion-host-1:~$ ssh host-3.example.org
jemurray@host-3:~ $
```

Another option is to use the more complex `-o ProxyCommand=` option:

```
jemurray@jasons-mbp:~ $ ssh -o ProxyCommand="ssh -W %h:%p bastion-host-1.example.org" host-3.example.org
jemurray@host-3:~ $
```

### The New Way

Starting with `ssh` version 7.3, the `-J` (jump host) option was introduced.  Users no longer need to perform a manual double-hop or remember the complex `ProxyCommand` syntax.

Connecting to a remote server with a single bastion:

```
jemurray@jasons-mbp:~ $ ssh -A -J bastion-host-1.example.org host-3.example.org
jemurray@host-3:~ $
```

Connecting to a remote server with multiple bastion hosts:


```
jemurray@jasons-mbp:~ $ ssh -A -J bastion-host-1.example.org,bastion-host-2.example.org host-3.example.org
jemurray@host-3:~ $
```

Connecting to a remote server with multiple bastion hosts and different usernames:


```
jemurray@jasons-mbp:~ $ ssh -A -J user1@bastion-host-1.example.org,user2@bastion-host-2.example.org user3@host-3.example.org
jemurray@host-3:~ $
```
