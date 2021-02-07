---
title: "Using nsupdate to make dynamic DNS changes"
date: 2020-06-09T09:49:33-05:00
toc: false
images:
categories:
  - tech
tags:
  - nsupdate
  - dns
---

The command, `nsupdate` is used to perform dynamic DNS updates from the CLI.


Before the dynamic update:

```
jemurray@linux-host:~ $ host dyntest.jason.example.com
Host dyntest.jason.example.com not found: 3(NXDOMAIN)
```

Running the dynamic update command:

```
jemurray@linux-host:~$ nsupdate
> server 192.168.0.1
> update add dyntest.jason.example.com 86400 A 127.0.0.1
> send
```

If the dynamic updates fails, verify there are no ACL's prohibiting dynamic updates:

```
May 11 08:14:20 192.168.0.1 named[31289]: client 192.168.3.4#61452: update 'jason.example.com/IN' denied
```


The allow-update line is used to control which IP addresses are allowed to send dynamic updates:

```
zone "jason.example.com" in { # jason.example.com
  type master;
  database infoblox_zdb;
  masterfile-format raw;
  file "azd/db.jason.example.com._default";
  allow-update { key DHCP_UPDATER_default; 172.16.0.0/12; 10.0.0.0/8; };
  notify yes;
 };
 ```

Validate the change executed properly:

```
jemurray@linux-host:~$ host dyntest.jason.example.com
dyntest.jason.example has address 127.0.0.1
```
