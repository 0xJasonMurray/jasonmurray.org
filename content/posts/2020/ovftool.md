---
title: "Using ovftool to copy VM hosts"
date: 2020-04-19T14:57:12-05:00
categories:
  - tech
tags: [til, esxi, ovftool]
---
OVFTool can be used to copy VM hosts from one ESXi server to another via the CLI.

Download [OVF Tool](https://www.vmware.com/support/developer/ovf/) 


```
jemurray@home-server:~$ ovftool -ds=datastore0 vi://root@192.168.86.55/VIRL vi://root@192.168.86.2
Enter login information for source vi://192.168.86.55/
Username: root
Password: *********
Opening VI source: vi://root@192.168.86.55:443/VIRL
Enter login information for target vi://192.168.86.2/
Username: root
Password: *********
Opening VI target: vi://root@192.168.86.2:443/
Deploying to VI: vi://root@192.168.86.2:443/
```

-ds = the destination datastore name
