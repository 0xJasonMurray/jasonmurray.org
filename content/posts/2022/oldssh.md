---
title: "Connecting to old ssh servers with unsupported key exchange and cipher types"
date: 2022-01-18T10:16:22-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ssh
description: Temporarily fix key exchange and cipher errors when connecting to old ssh servers.
---

For those of us who support old hosts with old instances of `ssh` (quite common on old networking hardware), you may encounter the following errors:

```
jemurray@phalanges:~ $ ssh host1.example.com
Unable to negotiate with 192.168.0.1 port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1
```

```
jemurray@phalanges:~ $ ssh host2.example.com
Unable to negotiate with 192.168.0.2 port 22: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1
```

```
jemurray@phalanges:~ $ ssh host3.example.com
Unable to negotiate with 192.168.0.3 port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1
```

```
jemurray@phalanges:~ $ ssh -oKexAlgorithms=diffie-hellman-group1-sha1 host2.example.com
Unable to negotiate with 192.168.0.2 port 22: no matching cipher found. Their offer: aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc
```

## Temporary Fix

To temporarily fix these problems, use following command-line options:

- `-oKexAlgorithms=`: To specify the key exchange algorithms to use.
- `-c`: To specify the cipher to use.

For example, to fix all of the problems listed above:

```
jemurray@phalanges:~ $ ssh -oKexAlgorithms=diffie-hellman-group1-sha1 -c aes256-cbc host2.example.com
Password:
jemurray@host2~>
```

## Permanent Fix

A more "permanent" fix requires adding the following lines to the `~/.ssh/config` file:

```
# Single Host
Host host2.example.com
  KexAlgorithms diffie-hellman-group1-sha1
  Ciphers aes256-cbc

# Multiple Hosts
Host *.example.com
  KexAlgorithms diffie-hellman-group1-sha1
  Ciphers aes256-cbc

# Multiple KexAlgorithms and Cipers, separted by commas, can be specified as needed
Host *.example.org
  KexAlgorithms diffie-hellman-group1-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1
  Ciphers aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc
```

## Important Notes

I urge you *NOT* to follow this guide!

It is critical to keep `ssh` servers up to date!

**Don't use these temporary options unless absolutely necessary.**  

Upgrade, upgrade, upgrade!

