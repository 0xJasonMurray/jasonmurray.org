---
title: "Install Zeek packages with zkg"
date: 2020-10-17T09:33:18-05:00
toc: false
images:
categories:
  - tech
tags: 
  - zeek
  - zkg
---

# Overview

[Previously](/posts/2020/installzeek/) we installed Zeek (with Python3 support).  Today we will extend Zeek with 3rd party community scripts and plugins .  To facilitate searching for and installing packages, the Zeek community developed the [Zeek package manager](https://packages.zeek.org/) software.  

# Details

Ubuntu 20.04 is the base operating system Zeek is installed on.  There is no longer support for pip in Python 2.  Python3 is required:

```
sudo apt install python3-pip
```

Install `zkg` in /usr/local/bin:

```
sudo pip3 install zkg
```

Start the basic config:

```
jemurray@zeek:~$ sudo zkg autoconfig
Successfully wrote config file to /root/.zkg/config
```

Install a package:

```
jemurray@zeek:~$ sudo zkg install geoip-conn
The following packages will be INSTALLED:
  zeek/brimsec/geoip-conn (master)

Proceed? [Y/n] y
Installing "zeek/brimsec/geoip-conn".....
Installed "zeek/brimsec/geoip-conn" (master)
Loaded "zeek/brimsec/geoip-conn"
```

To automatically load all installed packages, add the following line to the end of `/usr/local/zeek//share/zeek/site/local.zeek`:

```
@load packages
```

To active a new package it must be deployed with `zeek-ctl`: 

```
[ZeekControl] > deploy
checking configurations ...
installing ...
removing old policies in /usr/local/zeek/spool/installed-scripts-do-not-touch/site ...
removing old policies in /usr/local/zeek/spool/installed-scripts-do-not-touch/auto ...
creating policy directories ...
installing site policies ...
generating standalone-layout.zeek ...
generating local-networks.zeek ...
generating zeekctl-config.zeek ...
generating zeekctl-config.sh ...
stopping ...
stopping zeek ...
starting ...
starting zeek ...
```


The package we installed extends the basic conn.log to include geo-location information for each IP address:

```
jemurray@zeek:/usr/local/zeek/logs/current$ cat conn.log | zeek-cut -d ts uid id.orig_h id.resp_h id.resp_p service geo.resp.country_code | zeek-column | grep -v US | grep -v "\-$"| head -10
2020-10-15T17:14:10+0000  CKcbeT3K8oOXBvxvpc  99.28.31.196                             52.114.88.29                             443    -     GB
2020-10-15T17:14:14+0000  CTheo33NgZrLQO9xf5  99.28.31.196                             20.191.46.109                            443    -     IE
2020-10-15T17:14:15+0000  Cg4D8l4CtQPATvb0pi  2600:1700:1391:411f:506d:fd48:cdb0:ae06  2a00:86c0:2040::1                        443    -     GB
2020-10-15T17:14:22+0000  C9hnQk1YG4AMaKRw1f  2600:1700:1391:411f:48c6:d6a1:5d99:6a40  2a03:2880:f018:0:face:b00c:0:2           443    ssl   IE
2020-10-15T17:14:22+0000  Cwdg144eSlulV8Apj   99.28.31.196                             52.16.169.9                              443    -     IE
2020-10-15T17:14:23+0000  CRust81CCiXsRPFEwa  2600:1700:1391:411f:48c6:d6a1:5d99:6a40  2a03:2880:f018:0:face:b00c:0:2           443    ssl   IE
2020-10-15T17:14:22+0000  CQVNbe1hgMYNqsGLJg  99.28.31.196                             52.49.5.216                              443    ssl   IE
2020-10-15T17:14:53+0000  CZuLWt49fjzU5fmRBi  192.168.86.24                            52.49.5.216                              443    -     IE
2020-10-15T17:14:10+0000  CAsFEEdkswNa8JnQ4   99.28.31.196                             52.114.76.34                             443    ssl   IE
2020-10-15T17:14:45+0000  C4bTzw5YVal8sMqmb   2600:1700:1391:411f:506d:fd48:cdb0:ae06  2a00:86c0:2040::1                        443    ssl   GB
```
