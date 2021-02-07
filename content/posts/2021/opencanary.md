---
title: "Install a Opencanary honeypot on Debian 10"
date: 2021-01-28T06:14:11-06:00
toc: false
images:
categories:
  - tech
tags: 
  - debian
  - opencanary
  - honeypot
  - security
---

# Overview

A honeypot is a server lying in wait for rogue actors to interrogate it's services.  These servers allow a security team to detect attempts to scan for open services running within the connected network.   Honeypots listen on ports which simulate commonly attacked services such as `rdp`, `smb`, `ssh`, etc.   Under normal circumstances, these servers do not receive any connection attempts.  Communication with honeypot services will generate an alert signifying a possible break-in attempt.   

[Opencanary](https://github.com/thinkst/opencanary/tree/master/opencanary) is an open source version of the [Thinkst Canary](https://canary.tools/) commercial Canary (honeypot) product.  This document details the installation and configuration of the Opencanary software running in a [Docker](https://www.docker.com/) container on a Debian 10 host.  


# Details

[Install Docker](https://docs.docker.com/engine/install/) on a supported operating system.  In this guide we will be working on [Debian 10](https://docs.docker.com/engine/install/debian/) with Docker already installed.

Clone the [Opencanary GIT](https://github.com/thinkst/opencanary) repository:

```
git clone https://github.com/thinkst/opencanary.git
```

Build the Opencanary docker image:

```
cd docker 
sudo docker build -t opencanary -f Dockerfile.stable .
```

Validate the Docker image installed:

```
jemurray@shell:~/opencanary$ sudo docker images
REPOSITORY    TAG          IMAGE ID       CREATED              SIZE
opencanary    latest       f7927f268d03   About a minute ago   1.12GB
```

Run and configure Opencanary:

```
sudo docker run -it opencanary /bin/bash
root@1395f3cfcbee:/opencanary#
```

Create the configuration:

```
root@f5159947ffb1:~# opencanaryd --copyconfig
[*] A sample config file is ready /etc/opencanaryd/opencanary.conf

[*] Edit your configuration, then launch with "opencanaryd --start"
```

Edit the configuration file `/etc/opencanaryd/opencanary.conf`.  This is a significant advantage of the `opencanary` honeypot, many common services are built into this software.  There is no need to download, configure, and install separate honeypots.  Enable a service by changing `false` to `true`: 

```
root@96c045d16184:~# grep enable /etc/opencanaryd/opencanary.conf
    "git.enabled": false,
    "ftp.enabled": true,
    "http.enabled": false,
    "httpproxy.enabled" : false,
    "portscan.enabled": true,
    "smb.enabled": true,
    "mysql.enabled": true,
    "ssh.enabled": true,
    "redis.enabled": false,
    "rdp.enabled": false,
    "sip.enabled": false,
    "snmp.enabled": false,
    "ntp.enabled": false,
    "tftp.enabled": false,
    "tcpbanner.enabled": false,
    "telnet.enabled": false,
    "mssql.enabled": false,
    "vnc.enabled": false,
```

Docker container are immutable, all changes are lost on shutdown.  A new docker container must be created from the container where the changes were made.  Run `docker ps -l` to find the `container id` of the previous docker instance:

```
jemurray@shell:~/opencanary$ sudo docker ps -l
CONTAINER ID   IMAGE        COMMAND       CREATED         STATUS                       PORTS     NAMES
67b16dfa404c   opencanary   "/bin/bash"   6 minutes ago   Exited (130) 3 seconds ago             flamboyant_lamarr
```

`commit` the new configuration changes:

```
jemurray@shell:~/opencanary$ sudo docker commit 67b16dfa404c opencanary:latest
sha256:4f13dff52540f5f0109b2f46deec70c1f33f376440e3513a4947920122a6f50c
```

Run the new `docker` container, exposing `ftp` port to host OS with `-p 21:21` option:

```
jemurray@shell:~/opencanary$ sudo docker run -p 21:21 -it opencanary /bin/bash
root@9a7e2b38c7b6:~#
```

Start `opencanaryd`:

```
root@9a7e2b38c7b6:~# opencanaryd --start
** We hope you enjoy using OpenCanary. For more open source Canary goodness, head over to canarytokens.org. **
...
```


Tail the log file:

```
root@9a7e2b38c7b6:~# tail -f /var/tmp/opencanary.log
...
```


Connect to system with `ftp` client:

```
jemurray@shell:~$ ftp shell.jasonmurray.org
Trying 104.131.191.87...
Connected to shell.jasonmurray.org.
220 FTP server ready
Name (shell.jasonmurray.org:jemurray):
331 Password required for jemurray.
Password:
530 Sorry, Authentication failed.
Login failed.
ftp> quit
221 Goodbye.
```

Examine the log:

```
{"dst_host": "172.17.0.2", "dst_port": 21, "local_time": "2021-01-18 02:23:33.983203", "local_time_adjusted": "2021-01-18 02:23:33.983258", "logdata": {"PASSWORD": "test", "USERNAME": "jemurray"}, "logtype": 2000, "node_id": "opencanary-1", "src_host": "192.168.120.122", "src_port": 42614, "utc_time": "2021-01-18 02:23:33.983250"}
```

