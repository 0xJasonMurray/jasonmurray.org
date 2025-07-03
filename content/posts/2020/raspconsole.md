---
title: "Raspberry PI as a remote serial console"
date: 2020-07-23T12:45:10-05:00
toc: false
images:
categories:
  - tech
tags: 
  - rapsberrypi
  - serialconsole
---

Build a multi-port remote serial console for under $100 with a Raspberry PI and a few USB serial cables:

![piserial](/images/raspberrypiserialconsole.jpg)

Parts:

- Raspberry PI
- USB power supply
- USB serial cables
- Devices that require a serial console

Download and install the Raspberry PI OS following these instructions: https://www.raspberrypi.org/downloads/raspberry-pi-os/

Using a keyboard and monitor, log in with the `pi` username and change the password:

```
pi@raspberrypi:~ $ passwd
Changing password for pi.
Current password:
New password:
Retype new password:
Password changed
```

Enable remote `ssh` access:

```
pi@raspberrypi:~ $ sudo systemctl enable ssh
Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh

pi@raspberrypi:~ $ sudo systemctl start ssh
```

SSH into the Raspberry PI:

```
jemurray@mbp-2019:~ $ ssh pi@192.168.86.6
pi@192.168.86.6's password:
Linux raspberrypi 4.19.118+ #1311 Mon Apr 27 14:16:15 BST 2020 armv6l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Jul 23 18:58:21 2020 from 192.168.86.248
pi@raspberrypi:~ $
```

Determine what device the USB serial cable is registered as.  Before plugging in the serial cable, look at all the `/dev/tty*` files:

```
pi@raspberrypi:~ $ ls /dev/tty*
/dev/tty    /dev/tty19  /dev/tty3   /dev/tty40  /dev/tty51  /dev/tty62
/dev/tty0   /dev/tty2   /dev/tty30  /dev/tty41  /dev/tty52  /dev/tty63
/dev/tty1   /dev/tty20  /dev/tty31  /dev/tty42  /dev/tty53  /dev/tty7
/dev/tty10  /dev/tty21  /dev/tty32  /dev/tty43  /dev/tty54  /dev/tty8
/dev/tty11  /dev/tty22  /dev/tty33  /dev/tty44  /dev/tty55  /dev/tty9
/dev/tty12  /dev/tty23  /dev/tty34  /dev/tty45  /dev/tty56  /dev/ttyAMA0
/dev/tty13  /dev/tty24  /dev/tty35  /dev/tty46  /dev/tty57  /dev/ttyprintk
/dev/tty14  /dev/tty25  /dev/tty36  /dev/tty47  /dev/tty58
/dev/tty15  /dev/tty26  /dev/tty37  /dev/tty48  /dev/tty59
/dev/tty16  /dev/tty27  /dev/tty38  /dev/tty49  /dev/tty6
/dev/tty17  /dev/tty28  /dev/tty39  /dev/tty5   /dev/tty60
/dev/tty18  /dev/tty29  /dev/tty4   /dev/tty50  /dev/tty61
```

Plug in the serial console cable and look at the `/dev/tty*` files again.  There is a new file: `/dev/ttyUSB0`, this is the file we are looking for:

```
pi@raspberrypi:~ $ ls /dev/tty*
/dev/tty    /dev/tty19  /dev/tty3   /dev/tty40  /dev/tty51  /dev/tty62
/dev/tty0   /dev/tty2   /dev/tty30  /dev/tty41  /dev/tty52  /dev/tty63
/dev/tty1   /dev/tty20  /dev/tty31  /dev/tty42  /dev/tty53  /dev/tty7
/dev/tty10  /dev/tty21  /dev/tty32  /dev/tty43  /dev/tty54  /dev/tty8
/dev/tty11  /dev/tty22  /dev/tty33  /dev/tty44  /dev/tty55  /dev/tty9
/dev/tty12  /dev/tty23  /dev/tty34  /dev/tty45  /dev/tty56  /dev/ttyAMA0
/dev/tty13  /dev/tty24  /dev/tty35  /dev/tty46  /dev/tty57  /dev/ttyprintk
/dev/tty14  /dev/tty25  /dev/tty36  /dev/tty47  /dev/tty58  /dev/ttyUSB0
/dev/tty15  /dev/tty26  /dev/tty37  /dev/tty48  /dev/tty59
/dev/tty16  /dev/tty27  /dev/tty38  /dev/tty49  /dev/tty6
/dev/tty17  /dev/tty28  /dev/tty39  /dev/tty5   /dev/tty60
/dev/tty18  /dev/tty29  /dev/tty4   /dev/tty50  /dev/tty61
```

Install the `cu` utility.  This command will be used to connect to the serial console:

```
pi@raspberrypi:~ $ sudo apt install cu
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  cu
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
```

Connect to the serial console:

```
pi@raspberrypi:~ $ cu -l /dev/ttyUSB0
Connected.

cisco-sw-home con0 is now available

Press RETURN to get started.

***********************************************************************
*        WARNING - Unauthorized access to or use of this system is    *
*                         strictly prohibited.                        *
*  Use of this system is subject to all policies and procedures set   *
*  forth by the Example Company located at                            *
*  https://example.com/about/compliance-policies. Unauthorized use is *
*  prohibited and may result in administrative or legal action. The   *
*  Example Company may monitor the use of this system.                *
***********************************************************************


User Access Verification

Username:
```

To exit out of the serial console type (two tildes and a period).  If you only use one tilde it will drop the SSH session to the Raspberry PI:

```
<enter>~~.
```

Multiple USB serial cables can be attached to the Raspberry PI:

```
pi@raspberrypi:~ $ ls -al /dev/ttyUSB*
crw-rw---- 1 root dialout 188, 0 Jul 23 18:58 /dev/ttyUSB0
crw-rw---- 1 root dialout 188, 1 Jul 23 19:00 /dev/ttyUSB1
```

Change the options to the `cu` command to connect to the other serial ports:

```
cu -l /dev/USB1
Connected.

cisco-sw-home con0 is now available

Press RETURN to get started.
```

By default the serial speed is 9600bps, this can be adjusted with the `-s` option, for example 115,200bps:

```
pi@raspberrypi:~ $ cu -s 115200 -l /dev/ttyUSB0
Connected.
```