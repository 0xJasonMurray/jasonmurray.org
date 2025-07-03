---
title: "Installing iperf3 on a Cisco 9K"
date: 2020-11-05T08:13:12-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - iperf3
  - docker
  - apphosting
---

# Overview

Running a custom built `iperf3` Docker image on the Cisco Catalyst 9300 series platform.

Reference guide: https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/prog/configuration/171/b_171_programmability_cg/application_hosting.html

# Create the Docker image

Create the Dockerfile:

```
FROM alpine:latest

MAINTAINER Jason Murray

# build intial apk binary cache and install iperf3
RUN apk add --no-cache iperf3 \
    && adduser -S iperf

USER iperf

# Expose the default iperf3 server ports
EXPOSE 5201/tcp 5201/udp

# entrypoint allows you to pass your arguments to the container at runtime
# very similar to a binary you would run. For example, in the following
# docker run -it <IMAGE> --help' is like running 'iperf --help'
ENTRYPOINT ["iperf3"]

# iperf3 -s = run in Server mode
CMD ["-s"]
```

Create the Docker image:

```
jemurray@home-server:~/iperf3-docker$ sudo docker build --tag iperf3 .
[sudo] password for jemurray:
Sending build context to Docker daemon  2.048kB
Step 1/7 : FROM alpine:latest
 ---> 2f41f701ab2c
Step 2/7 : MAINTAINER Jason Murray
 ---> Running in 1d76a0e63c25
Removing intermediate container 1d76a0e63c25
 ---> 3fe72b960a85
Step 3/7 : RUN apk add --no-cache iperf3     && adduser -S iperf
 ---> Running in 815ed6d5a3d9
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/1) Installing iperf3 (3.7-r2)
Executing busybox-1.31.1-r16.trigger
OK: 53 MiB in 25 packages
Removing intermediate container 815ed6d5a3d9
 ---> 1e0d4e7eced8
Step 4/7 : USER iperf
 ---> Running in 888cd03dc0de
Removing intermediate container 888cd03dc0de
 ---> 0dc068860223
Step 5/7 : EXPOSE 5201/tcp 5201/udp
 ---> Running in 735799ac98b1
Removing intermediate container 735799ac98b1
 ---> 56dc5022ddc5
Step 6/7 : ENTRYPOINT ["iperf3"]
 ---> Running in c44a9d5d54b0
Removing intermediate container c44a9d5d54b0
 ---> 0416933990d1
Step 7/7 : CMD ["-s"]
 ---> Running in 945d74959f5d
Removing intermediate container 945d74959f5d
 ---> 829735b1082f
Successfully built 829735b1082f
Successfully tagged iperf3:latest
```

Validate image creation:

```
jemurray@home-server:~/iperf3-docker$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
iperf3              latest              829735b1082f        About a minute ago   51.1MB
```

Test Docker image server:

```
jemurray@home-server:~/iperf3-docker$ sudo docker run -p 5201:5201/tcp -it iperf3 '-s'
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
```

Validate a client can connect:

```
jemurray@mbp-2019:~ $ iperf3 -c 192.168.86.5
Connecting to host 192.168.86.5, port 5201
[  5] local 192.168.86.33 port 62445 connected to 192.168.86.5 port 5201
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec  10.5 MBytes  88.0 Mbits/sec
[  5]   1.00-2.00   sec  11.2 MBytes  94.0 Mbits/sec
[  5]   2.00-3.00   sec  8.30 MBytes  69.6 Mbits/sec
[  5]   3.00-4.00   sec  9.66 MBytes  81.1 Mbits/sec
[  5]   4.00-5.00   sec  13.7 MBytes   115 Mbits/sec
[  5]   5.00-6.00   sec  12.0 MBytes   101 Mbits/sec
[  5]   6.00-7.00   sec  14.3 MBytes   119 Mbits/sec
[  5]   7.00-8.00   sec  14.6 MBytes   123 Mbits/sec
[  5]   8.00-9.00   sec  11.2 MBytes  94.1 Mbits/sec
[  5]   9.00-10.00  sec  13.7 MBytes   115 Mbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.00  sec   119 MBytes   100 Mbits/sec                  sender
[  5]   0.00-10.06  sec   118 MBytes  98.4 Mbits/sec                  receiver

iperf Done.
```

Save (export) the Docker image into a `tar` file:

```
jemurray@home-server:~/iperf3-docker$ sudo docker save iperf3 > iperf3.tar
```

Validate the exported file:

```
jemurray@home-server:~/iperf3-docker$ ls -al iperf3.tar
-rw-rw-r-- 1 jemurray jemurray 53521920 Oct 22 08:23 iperf3.tar
```

# Configure the 9k switch

**IMPORTANT:** App Containers will **NOT** work without the Cisco 128G SSD drive.  You can **NOT** use any other type of USB storage.


Attempting to install apps without the drive results in the following error:

```
*Oct 22 16:31:40.823: %IM-6-INSTALL_MSG: Switch 1 R0/0: ioxman: app-hosting: Install failed: Installing apps on system flash is not allowed.
```


Insert the 128G drive:

![Cisco SSD drive](/images/ciscossd.jpg)

```
*Nov  3 01:30:10.381: %IOSD_INFRA-6-IFS_DEVICE_OIR: Device usbflash1 added
*Nov  3 01:30:10.361: %IOSXE-0-PLATFORM: Switch 1 R0/0: udev: usb1: has been inserted
```

Validate the drive exists:

```
9300-1#dir usbflash1:
Directory of usbflash1:/

11      drwx            16384   Nov 3 2020 01:30:07 +00:00  lost+found

118014062592 bytes total (111933124608 bytes free)
```

Validate the filesystem is `ext4`:

```
9300-1#show usbflash1: filesys
Filesystem: usbflash1
Filesystem Path: /vol/usb1
Filesystem Type: ext4
Mounted: Read/Write
```

Enable IOx service:

```
9300-1(config)#iox
```

Validate IOx is running (wait until they all say Running):

```
9300-1#show iox-service

IOx Infrastructure Summary:
---------------------------
IOx service (CAF) 1.8.0.3 : Running
IOx service (HA)          : Running
IOx service (IOxman)      : Running
Libvirtd   1.3.4          : Running
Dockerd    18.03.0        : Running
```


Note: In this lab, the management interface is used for the Docker container.  The front ports or VLAN bridges could be used if desired:

```
interface GigabitEthernet0/0
 vrf forwarding Mgmt-vrf
 ip address dhcp
 negotiation auto
 ```

Configure the app-hosting interface and Docker options:

```
9300-1(config)#app-hosting appid iperf3
9300-1(config-app-hosting)#app-vnic management guest-interface 0
9300-1(config-app-hosting)#start
9300-1(config-app-hosting)#app-resource docker
9300-1(config-app-hosting-docker)#run-opts 1 "--restart=unless-stopped -p 5201:5201/tcp -p 5201:5201/udp"
```

Copy the Docker container to the 9K `flash:` file system:

```
jemurray@home-server:~/iperf3-docker$ scp iperf3.tar 192.168.86.44:iperf3.tar
Password:
iperf3.tar   100%   51MB   1.4MB/s   00:35
```

Install the iperf3 Docker image:

```
9300-1#app-hosting install appid iperf3 package flash:iperf3.tar
Installing package 'flash:iperf3.tar' for 'iperf3'. Use 'show app-hosting list' for progress.
```

Check the progress:

```
9300-1#show app-hosting list
App id                                   State
---------------------------------------------------------
iperf3                                   RUNNING
```

If `show app-hosting list` fails, check for this error on console.  Validate the proper Cisco SSD drive is installed in the rear USB port:

```
*Oct 22 16:38:24.474: %IM-6-INSTALL_MSG: Switch 1 R0/0: ioxman: app-hosting: Install failed: Installing apps on system flash is not allowed.
```

Connect to the Docker image console and retrieve the current IP address:

Note: In a real production environment, static addressing or VLAN bridging would be the desired setup.

```
9300-1#app-hosting connect appid iperf3 session /bin/sh
/ $ ifconfig
eth0      Link encap:Ethernet  HWaddr 52:54:DD:AC:8E:1C
          inet addr:192.168.86.220  Bcast:192.168.86.255  Mask:255.255.255.0
          inet6 addr: fe80::5054:ddff:feac:8e1c/64 Scope:Link
          inet6 addr: 2600:1700:1391:411f:5054:ddff:feac:8e1c/64 Scope:Global
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:246 errors:0 dropped:0 overruns:0 frame:0
          TX packets:14 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:62326 (60.8 KiB)  TX bytes:1876 (1.8 KiB)
```


Connect to the `iperf3` running on the 9K Docker image from a remote host:

```
jemurray@home-server:~$ iperf3 -c 192.168.86.220
Connecting to host 192.168.86.220, port 5201
[  4] local 192.168.86.5 port 60078 connected to 192.168.86.220 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec   114 MBytes   959 Mbits/sec    0    411 KBytes
[  4]   1.00-2.00   sec   113 MBytes   946 Mbits/sec    0    501 KBytes
[  4]   2.00-3.00   sec   112 MBytes   942 Mbits/sec    0    501 KBytes
[  4]   3.00-4.00   sec   112 MBytes   943 Mbits/sec    0    523 KBytes
[  4]   4.00-5.00   sec   112 MBytes   941 Mbits/sec    0    523 KBytes
[  4]   5.00-6.00   sec   112 MBytes   941 Mbits/sec    0    523 KBytes
[  4]   6.00-7.00   sec   112 MBytes   941 Mbits/sec    0    523 KBytes
[  4]   7.00-8.00   sec   112 MBytes   942 Mbits/sec    0    523 KBytes
[  4]   8.00-9.00   sec   112 MBytes   941 Mbits/sec    0    523 KBytes
[  4]   9.00-10.00  sec   112 MBytes   936 Mbits/sec   80    424 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec  1.10 GBytes   943 Mbits/sec   80             sender
[  4]   0.00-10.00  sec  1.10 GBytes   941 Mbits/sec                  receiver

iperf Done.
```

