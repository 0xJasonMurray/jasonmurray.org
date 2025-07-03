---
title: "How to Install Debian GNU/Linux on the Toshiba 2545XCDT"
date: 2000-06-13T19:10:23-06:00
toc: false
images:
categories:
  - tech
tags: 
  - toshiba2545
  - laptop
---

```
How to Install Debian GNU/Linux on the Toshiba 2545XCDT
-------------------------------------------------------

Author: Jason E. Murray
Date: 6/13/00
Notes: This document is quite dated, but left here for reference. 2002.8.30

- Backup your current system (if it exists).

- Build the disks
	- Get the boot/root/driver disks from ftp.debian.org
	- I prefer the unstable release (newest)
	- $ dd if=(disk) of=/dev/fd0

- Inital Bootup
	- Boot from rescue disk
	- Insert root floppy when prompted
	
- Inital Configuration Menus
	- Configure Keyboard (US/Qwerty)
	- Partition Disk
		/dev/hda1 = Win98 (2GB)
			-Bootable
		/dev/hda2 = Linux Swap (131MB)
		/dev/hda3 = Linux Ext2 (2.1GB)
	- Initalize Swap
	- Initalize Linux Partition
		- Check for bad blocks
		- Block Size 4096; Frag Size=4096
		  265472 Inodes; 530145 blocks
		  5.0% reserved; 
		  Backup Superblocks: 32768, 98304, 163840, 229376, 294912
	- Mount / filesystem
	- Install OS Kernel and modules
		- Use Resuce disk to get kernel
		- Use Drivers disk 1-3 for modules
	- Configure device driver modules
		- Nothing to configure in here
	- Configure PCMCIA
		- Select Intel i82365
	- Configure the hostname
		- Set to LAPTUX
	- Configure the Network
		- eth0 is pcmcia
		- ip = 208.176.45.82
		- netmask = 255.255.255.240
		- gateway = 208.176.45.81
	- Install Base System
		- Install via network (http)
		- Retreive base2_2.tgz from http.us.debian.org
		- path: debian/dists/potato/main/disks-i386/current
		- Potato release
	- Configure the base system
		- Configure TimeZone
			- US -> Central.
		- Hardware clock set to local time
	- Make linux boot directly from hard disk
		- Install LILO in MBR (/dev/hda)
	- Make boot floppy
		- I would do this!
	- Reboot System

Configuring the New System

	- Install MD5 Passwords
	- Install Shadow Passwords
	- Set root password
	- Setup a non-root account (jemurray)
	- Dont use PPP to configure the new system
		- Use Network
	- APT Configuration
		- Use http for apt GET method
		- Allow non-US, non-free.
		- Contry: US
		- Use: http.us.debian.org
		- No Proxy
		- No more APT sources
	- Packages to Install (System Configuration)
		- Choose ADVANCED
		- ...Learn how to use dselect...
		- To begin with I just let is select the standard
		  utilities, then I will add as I need them.

This is all there is to the "generic installation".  

Debian is by far the best GNU/Linux distribution that I have used so far.  The
package management utility (dselect) makes this the easiest system to keep
updated.  HOWEVER, I would suggest that if you do not know how to dowload,
configure, compile software yourself, that you first learn it before you
starting relying on dselect to do it for you...



Additional Information
----------------------

- Upgraded kernel to 2.2.16
	- Required the kernel source
	- Requires the pcmcia modules
		- These are not built into the kernel you need the 
		  pcmcia-cs package in order to get pcmcia cards working
	- enabled APM support - debian has this turned off by default.

- Audio Configuration
	- I copied the /etc/modules.conf from the Documentation for the OPL3-SA2 that cames with the kernel

		  alias char-major-14 opl3sa2
		  pre-install opl3sa2 modprobe "-k" "ad1848"
		  post-install opl3sa2 modprobe "-k" "opl3"
		  options opl3sa2 io=0x220 mss_io=0x530 mpu_io=0x330 irq=5 dma=1 dma2=0

	- I also needed to change the audio device (/dev) permissions to 666
```