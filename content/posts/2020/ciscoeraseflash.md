---
title: "Erasing everything on Cisco IOS flash storage"
date: 2020-10-21T07:11:19-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - erase
  - flash
---

Files on the `flash:` storage before erasing:

3560sw-1#dir flash:
Directory of flash:/

    2  -rwx         736   Mar 1 1993 00:10:59 +00:00  vlan.dat
    3  drwx         512   Mar 1 1993 00:13:49 +00:00  c3560-ipservicesk9-mz.122-58.SE1
    5  -rwx         850   Mar 1 1993 00:07:11 +00:00  config.text
    6  -rwx           5   Mar 1 1993 00:07:11 +00:00  private-config.text
    7  -rwx        2072   Mar 1 1993 00:07:11 +00:00  multiple-fs

27998208 bytes total (9958400 bytes free)

Erasing flash:

```
3560sw-1#erase flash:
Erasing the flash filesystem will remove all files! Continue? [confirm]
mifs[4]: 0 files, 1 directories
mifs[4]: Total bytes     : 27998208
mifs[4]: Bytes used      : 1024
mifs[4]: Bytes available : 27997184
mifs[4]: mifs fsck took 0 seconds.

Erase of flash: complete
```


`flash:` is empty:

```
3560sw-1#dir flash:
Directory of flash:/

No files in directory

27998208 bytes total (27997184 bytes free)
```