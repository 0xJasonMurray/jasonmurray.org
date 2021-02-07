---
title: "Convert HEIC to JPG from the commandline"
date: 2020-09-24T06:13:08-05:00
toc: false
images:
categories:
  - tech
tags: 
  - imagemagick
  - mogrify
  - jpg
  - heic
---

By default pictures taken from an iPhone are stored in HEIC format.  I find that most of the systems I interact with do not support HEIC.  Converting a large number of photos is tedious with the traditional GUI applications.

ImageMagick has the `mogrify` command line tool to quickly batch convert any number of files with a single command.  ImageMagick is not installed by default.  Use brew to install if necessary: `brew install imagemagick`.


Files copied with AirDrop off an iPhone:

```
jemurray@mbp-2019:~/Downloads $ ls -al IMG_0956.HEIC IMG_0957.HEIC IMG_0954.HEIC IMG_0953.HEIC IMG_0951.HEIC IMG_0950.HEIC IMG_0949.HEIC IMG_0948.HEIC IMG_0946.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1526912 Sep 23 16:42 IMG_0946.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  2446142 Sep 23 16:43 IMG_0948.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1351051 Sep 23 16:43 IMG_0949.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1527961 Sep 23 16:43 IMG_0950.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1433100 Sep 23 16:44 IMG_0951.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  2107683 Sep 23 16:45 IMG_0953.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1804572 Sep 23 16:45 IMG_0954.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1725539 Sep 23 16:47 IMG_0956.HEIC
-rw-r--r--@ 1 jemurray  access_bpf  1642674 Sep 23 16:47 IMG_0957.HEIC
```

Use the `mogrify` command to convert the images from HEIC to JPG:

```
jemurray@mbp-2019:~/Downloads $ mogrify -format jpg IMG_0956.HEIC IMG_0957.HEIC IMG_0954.HEIC IMG_0953.HEIC IMG_0951.HEIC IMG_0950.HEIC IMG_0949.HEIC IMG_0948.HEIC IMG_0946.HEIC
```

When the conversion is complete, both the HEIC and JPG format images are available:

```
jemurray@mbp-2019:~/Downloads $ ls -alt 
total 111213776
-rw-r--r--@   1 jemurray  staff          2469609 Sep 23 17:09 IMG_0946.jpg
drwx------@ 544 jemurray  staff            17408 Sep 23 17:09 .
-rw-r--r--@   1 jemurray  staff          3373659 Sep 23 17:09 IMG_0948.jpg
-rw-r--r--@   1 jemurray  staff          2345537 Sep 23 17:09 IMG_0949.jpg
-rw-r--r--@   1 jemurray  staff          2743936 Sep 23 17:09 IMG_0950.jpg
-rw-r--r--@   1 jemurray  staff          2497650 Sep 23 17:09 IMG_0951.jpg
-rw-r--r--@   1 jemurray  staff          2844725 Sep 23 17:09 IMG_0953.jpg
-rw-r--r--@   1 jemurray  staff          2743487 Sep 23 17:09 IMG_0954.jpg
-rw-r--r--@   1 jemurray  staff          2694978 Sep 23 17:09 IMG_0957.jpg
-rw-r--r--@   1 jemurray  staff          2878063 Sep 23 17:09 IMG_0956.jpg
-rw-r--r--@   1 jemurray  access_bpf     1725539 Sep 23 16:47 IMG_0956.HEIC
-rw-r--r--@   1 jemurray  access_bpf     1642674 Sep 23 16:47 IMG_0957.HEIC
-rw-r--r--@   1 jemurray  access_bpf     1804572 Sep 23 16:45 IMG_0954.HEIC
-rw-r--r--@   1 jemurray  access_bpf     2107683 Sep 23 16:45 IMG_0953.HEIC
-rw-r--r--@   1 jemurray  access_bpf     1433100 Sep 23 16:44 IMG_0951.HEIC
-rw-r--r--@   1 jemurray  access_bpf     1527961 Sep 23 16:43 IMG_0950.HEIC
-rw-r--r--@   1 jemurray  access_bpf     1351051 Sep 23 16:43 IMG_0949.HEIC
-rw-r--r--@   1 jemurray  access_bpf     2446142 Sep 23 16:43 IMG_0948.HEIC
-rw-r--r--@   1 jemurray  access_bpf     1526912 Sep 23 16:42 IMG_0946.HEIC
```