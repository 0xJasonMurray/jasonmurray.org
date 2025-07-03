---
title: "Convert all HEIC images to jpeg with a single command"
date: 2023-02-11T09:58:06-06:00
toc: false
images:
categories:
  - tech
tags: 
  - heic
  - imagemagick
description: How to convert all Apple HEIC images to jpegs with a single command.
---

# Overview

Too many services still do not support HEIC formatted images. Install ImageMagick and run this command to convert them all.

# Technical Details

Add this to your ~/.bash_profile so that we don't have to memorize all the options:

```
alias convertallheic='for f in *.HEIC *.heic; do magick "$f" -quality 100% "$f.jpg"; done'
```

Source the file to load it. You don't have to do this all the time, terminals will typically load aliases on startup:

```
source ~/.bash_profile
```

Execute in a directory full of HEIC images:

```
convertallheic
```

Validate it worked:

```
jemurray@desktok:~/Downloads/test $ ls -al
total 30128
drwxr-xr-x    8 jemurray  staff      256 Feb 11 09:56 .
drwx------@ 288 jemurray  staff     9216 Feb 11 09:55 ..
-rw-r--r--@   1 jemurray  staff   854058 Dec 17 22:46 IMG_7140.HEIC
-rw-r--r--    1 jemurray  staff  4442482 Feb 11 09:56 IMG_7140.HEIC.jpg
-rw-r--r--@   1 jemurray  staff   779419 Dec 17 22:46 IMG_7141.HEIC
-rw-r--r--    1 jemurray  staff  4180318 Feb 11 09:56 IMG_7141.HEIC.jpg
-rw-r--r--@   1 jemurray  staff   853056 Dec 17 22:46 IMG_7145.HEIC
-rw-r--r--    1 jemurray  staff  4301712 Feb 11 09:56 IMG_7145.HEIC.jpg
```

