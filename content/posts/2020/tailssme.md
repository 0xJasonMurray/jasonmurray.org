---
title: "Tails Linux wipes active memory when the USB drive is removed"
date: 2020-06-08T07:48:46-05:00
toc: false
images:
tags: 
  - tails
  - security
  - usb
---

[Tails Linux](https://tails.boum.org/) will automatically shut down and [forcibly erase](https://tails.boum.org/contribute/design/memory_erasure/) memory of the running operating system if the USB drive is removed while powered on:

{{< youtube CU_0WEQHcRw >}}


Tails erases active memory by using a custom [udev watch-dog](https://gitlab.tails.boum.org/tails/tails/-/blob/master/config/chroot_local-includes/usr/src/udev-watchdog.c) binary:

```
amnesia@amnesia:/etc/udev/rules.d$ ps -ef | grep wat
root      5877     1  0 00:59 ?        00:00:00 /bin/sh /usr/local/lib/udev-watchdog-wrapper
root      5886  5877  0 00:59 ?        00:00:00 /usr/local/sbin/udev-watchdog /devices/pci0000:00/0000:00:14.0/usb2/2-1/2-1:1.0/host1/target1:0:0/1:0:0:0/block/sdb/sdb1 disk
```


Full details can be found [here](https://tails.boum.org/contribute/design/memory_erasure/).