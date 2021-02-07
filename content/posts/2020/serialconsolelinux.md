---
title: "Managing Ubuntu servers over a serial console"
date: 2020-12-07T15:26:38-06:00
toc: false
images:
categories:
  - tech
tags: 
  - serial
  - console
  - linux
  - ubuntu
---

# Overview

By configuring the physical serial port on a Linux server to listen for incoming connections, administrators can replace the traditional keyboard and monitor with a single serial cable.  Now system administrators can manage physical servers with a serial cable attached to a laptop or a [serial console server](/posts/2020/raspconsole/) over a remote SSH session.

Over the years, serial consoles have lost favor to *lights out management cards*, such as `iLo`, `iDrac`, or `ipmi`.  However, in organizations where serial management is still prevalent, such as data centers with large networking environments, serial consoles are a viable alternative to LoM and KVM.   


# Details

The first task is to determine the device file for the serial port.  On Linux, the serial port is typically `/dev/ttyS0`.  

Double check by looking at the `/dev/ttyS*` files.  Group ownership of physical serial ports may be `tty`:

```
jemurray@myserver:~$ ls -al /dev/ttyS*
crw-rw---- 1 root tty     4, 64 May 11  2019 /dev/ttyS0
```

Another option, is to examine the `/proc/tty/driver/serial` file.  Look for devices with `uart:16550A` in them:

```
jemurray@myserver:~$ sudo cat /proc/tty/driver/serial
serinfo:1.0 driver revision:
0: uart:16550A port:000003F8 irq:4 tx:1346 rx:172 brk:172 RTS|CTS|DTR|DSR
1: uart:16550A port:000002F8 irq:3 tx:0 rx:0 CTS
```

When in doubt, it does not hurt to try `/dev/ttyS0`.  If that does not work try the next `uart` from the list above (ie. `/dev/ttyS1`).



The process that listens on the serial port is called `getty`.  It opens a tty port, prompts for a login name and invokes the /bin/login command.  To enable `getty` on the serial port, create and add the following to the `/etc/init/ttyS0.conf` file:

(For Ubuntu 9.10 to current)

```
# ttyS0 - getty
#
# This service maintains a getty on ttyS0 from the point the system is
# started until it is shut down again.

start on stopped rc or RUNLEVEL=[2345]
stop on runlevel [!2345]

respawn
exec /sbin/getty -L 115200 ttyS0 vt102
```

(Ubuntu 6.x to 9.04)

```
# ttyS0 - getty
#
# This service maintains a getty on ttyS0 from the point the system is
# started until it is shut down again.

start on runlevel 1
start on runlevel 2
start on runlevel 3
start on runlevel 4
start on runlevel 5

stop on runlevel 0
stop on runlevel 6

respawn
exec /sbin/getty 115200 ttyS0
```

Then start the `getty` daemon:

```
sudo start ttyS0
```

The following process will start:

```
root      3480     1  0  2020 ttyS0    00:00:00 /sbin/getty -L 115200 ttyS0 vt102
```

Connecting to Linux from a serial console:

```
Connect to port > 17

Ubuntu 20.04.4 LTS myserver ttyS0

myserver login:
```

Using a serial cable attached to laptop is dependent on the operating system, for [macOS follow these instructions.](/posts/2020/serialconsolemacos/)
