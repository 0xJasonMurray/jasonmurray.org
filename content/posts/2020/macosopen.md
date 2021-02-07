---
title: "Opening applications from the command line in macOS"
date: 2020-05-30T11:10:46-05:00
toc: false
images:
categories:
  - tech
tags: 
  - macOS
  - open
  - cli
---

# Summary:

Open an app from the commandline:

```
open macosopen.md
```

# Details:

## Using a specific application

Open a specific application with the `-a` option.  

In the example below the `.` specifies the current working directory should be opened.   In Visual Studio Code, this is the equilivant to opening a `workspace`:

```
jemurray@mbp-2019:~/www $ open -a 'Visual Studio Code' .
jemurray@mbp-2019:~/www
```

![vscode open](/images/vscodeopen.png)


## Open multiple instances

The same application can be opened in a new instance by using the `-n` option.

By default applications such as `wireshark` do not open multiple instances, instead they close the current file and open a new one.   The `-n` creates a new application instance per open.

```
jemurray@mbp-2019:~ $ ls -al *pcap
-rw-r--r--  1 root      staff    121693 Apr 13 10:57 fmd.pcap
-rw-r--r--  1 jemurray  staff      1668 May 10 17:33 icmp-mtu.pcap
jemurray@mbp-2019:~ $

jemurray@mbp-2019:~ $ open -n icmp-mtu.pcap
jemurray@mbp-2019:~ $ open -n fmd.pcap
jemurray@mbp-2019:~ $
```

![open multiapp](/images/openmultiapp.png)