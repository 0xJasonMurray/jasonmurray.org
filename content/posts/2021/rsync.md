---
title: "Using rsync to move large datasets"
date: 2021-03-30T16:53:39-05:00
toc: false
images:
categories:
  - tech
tags: 
  - rsync
description: Using rsync to move large datasets
---

## Overview

When moving a single file from one host to another, traditional commands like `scp` or `sftp` do the job just fine. However, when large data sets are involved better tooling is required. 

The `rsync` command is design to transfer files and directories between network connected hosts or directories on a local file system. `rsync` uses the *rsync algorithm*, which only transfers parts of the file(s) that have changed. With the `archive` option, `rsync` will maintain time stamps, ownership, and links, making it an ideal tool for directory synchronization or backups.

## Details

To transfer files from one host to another use the following command. The `-av` argument enables the following options

- Recursively copy all directories
- Maintain symlinks
- Preserve permissions
- Preserve timestamps
- Preserve group information
- Preserve ownership information
- Preserve special files
- Enable verbose output

```text
rsync -av ./public/ shell.jasonmurray.org:backups
```

Example output:

```
jemurray@phalanges:~/Documents/www-personal/current/jasonmurray.org $ rsync -av ./public/ shell.jasonmurray.org:backups
building file list ... done
created directory backups
./
.DS_Store
404.html
android-chrome-192x192.png
android-chrome-384x384.png
android-chrome-512x512.png
apple-touch-icon.png
browserconfig.xml
favicon-16x16.png
favicon-32x32.png
favicon.ico
favicon_io.zip
humans.txt
index.html
index.xml
```