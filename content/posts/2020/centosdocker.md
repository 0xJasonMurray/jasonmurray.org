---
title: "Install CentOS 8 (latest) with Docker"
date: 2020-11-11T16:20:50-06:00
toc: false
images:
categories:
  - tech
tags: 
  - docker
  - centos
---

## Overview


I have a few production RedHat 8 servers, but no lab.  Before implementing a new configuration change on the production servers, validating correctness in the lab is a prudent idea.  In a previous life, I would download the ISO images, install, and test.

![](/images/2020-11-11-16-34-43.png)

11 Minutes!  Who has time for that?  Not to mention the 10+ minutes it will take to do the installation.

Enter Docker, the virtual machine containerizing system.  

## Details

I presume there is an offical CentOS 8 image:

```
jemurray@home-server:~$ sudo docker search centos
[sudo] password for jemurray:
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   6276                [OK]
```

Download the CentOS image:

```
jemurray@home-server:~$ sudo docker pull centos:latest
latest: Pulling from library/centos
3c72a8ed6814: Pull complete
Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
```

Connect to console:

```
jemurray@home-server:~$ sudo docker run -it centos /bin/bash
[root@02d6266fd971 /]#
[root@02d6266fd971 /]#
```

Lab built and tested in under 10 minutes.  Less time then it would have taken to download the CentOS 8 image.