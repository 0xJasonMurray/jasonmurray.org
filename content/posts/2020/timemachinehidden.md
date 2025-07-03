---
title: "Retrieving hidden files in TimeMachine"
date: 2020-08-23T19:09:25-05:00
toc: false
images:
categories:
  - tech
tags: 
  - macos
  - timemachine
---

A [few nights](/posts/2020/macosbash/) ago a missing `>` wiped out my .bash_profile file:

```
echo "export BASH_SILENCE_DEPRECATION_WARNING=1" > .bash_profile
```

This should be simple to recover from TimeMachine, except the Finder window does not display hidden (system) files.   

To retrieve a file like this, open a terminal window and navigate to the locally mounted TimeMachine folder in the `/Volumes` directory:

```
jemurray@mbp-2019:~ $ cd /Volumes/MurrayBackup/Backups.backupdb/mbp-2019/Latest/Macintosh\ HD\ -\ Data/Users/jemurray
```

The files are there:

```
jemurray@mbp-2019:/Volumes/MurrayBackup/Backups.backupdb/mbp-2019/Latest/Macintosh HD - Data/Users/jemurray $ ls -al .bash_profile
lrwxr-xr-x+ 1 jemurray  staff  32 May 23 16:05 .bash_profile -> Documents/dotfiles/.bash_profile
```

Copy the file back:

```
jemurray@mbp-2019:/Volumes/MurrayBackup/Backups.backupdb/mbp-2019/Latest/Macintosh HD - Data/Users/jemurray $ cp .bash_profile ~
```
