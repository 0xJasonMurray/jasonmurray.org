---
title: "Extracting files from a MS Windows, Nullsoft Installer self-extracting archive on Linux"
date: 2020-12-13T20:20:52-06:00
toc: false
images:
categories:
  - tech
tags: 
  - 7z
---

# TL;DR

Extract files from a Nullsoft self-extracting archive using, `7z`:

```
7z e myapp.exe
```

# Details

Given a Nullsoft self-extracting archive file:

```
jemurray@shell:~$ file myapp.exe
myapp.exe: PE32 executable (GUI) Intel 80386, for MS Windows, Nullsoft Installer self-extracting archive
```

Install the extraction software on Linux, in this example Debian:

```
sudo apt install p7zip-full
```

Extract files from the `.exe`:

```
jemurray@shell:~$ 7z e myapp.exe

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,1 CPU Intel(R) Xeon(R) Gold 6248 CPU @ 2.50GHz (50657),ASM,AES-NI)

Scanning the drive for archives:
1 file, 49824644 bytes (48 MiB)

Extracting archive: myapp.exe
--
Path = myapp.exe
Type = Nsis
Physical Size = 49824644
Method = Deflate
Solid = -
Headers Size = 102546
Embedded Stub Size = 57856
SubType = NSIS-3 Unicode BadCmd=11

Everything is Ok

Files: 9
Size:       50033887
Compressed: 49824644
```

Extracted files:

```
jemurray@shell:~$ ls -al
total 97540
drwxr-xr-x  2 jemurray jemurray     4096 Dec 13 13:37  .
drwxr-xr-x 27 jemurray jemurray     4096 Dec 13 13:37  ..
-rw-r--r--  1 jemurray jemurray 49323645 Dec  4 17:47  app-64.7z
-rw-r--r--  1 jemurray jemurray     6656 Dec 13 13:22  nsExec.dll
-rw-r--r--  1 jemurray jemurray   434176 Dec 13 13:22  nsis7z.dll
-rw-r--r--  1 jemurray jemurray     4608 Dec 13 13:22  nsProcess.dll
-rw-r--r--  1 jemurray jemurray 49824644 Dec 13 13:22  myapp.exe
-rw-r--r--  1 jemurray jemurray     9216 Dec 13 13:22  SpiderBanner.dll
-rw-r--r--  1 jemurray jemurray   102400 Dec 13 13:22  StdUtils.dll
-rw-r--r--  1 jemurray jemurray    12288 Dec 13 13:22  System.dll
-rw-r--r--  1 jemurray jemurray   137826 Dec  4 17:47 'Uninstall myapp.exe'
-rw-r--r--  1 jemurray jemurray     3072 Dec 13 13:22  WinShell.dll
```