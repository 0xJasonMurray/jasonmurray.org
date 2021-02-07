---
title: "Multiple search options in find"
date: 2020-12-12T22:16:21-06:00
toc: false
images:
categories:
  - tech
tags: 
  - find
---

In CTF game I am playing, one of my tasks is to `find` a file with a specific name, size, and owner deep within a directory tree.  `find` is the logical command, but what are the options?

Using the `-a` option, multiple search criteria are chained together with logical `and`: 

```
jemurray@shell:~$ find . -type f -a -name '*foo*' -a -size +5G -a -size -20G -a -user jemurray -ls
   131244 14498696 -rw-r--r--   1 jemurray jemurray 14846652416 Dec 13 01:49 ./14G-foo.img
```



