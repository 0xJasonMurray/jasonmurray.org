---
title: "CLI to turn a directory of images into Markdown links"
date: 2021-03-15T06:14:54-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cli
  - bash
  - markdown
  - awk
  - linux
description:
---

## Overview

Convert a directory of images to clickable Markdown links:


```
for i in `IMG_17*.jpg`; do echo $i | awk '{print "[![Image of philmont prep hike](/images/" $1" )](/images/" $1 ")"}'; done
```

## Details

- `for i in...` -> Loop over each file, placing filename in the variable `i`.
- `ls -1 IMG_17*.jpg` -> List files matching the filename pattern. Then feed into the `for` loop.
- `echo $i` -> Print the variable `i`.
- awk (There are 2 key points, printing and variable expansion):
  - `print` -> Print everything after this statement to the screen. Text between `"`'s is printed as written. `$` (variables) are expanded and printed.
  - `$1` -> Print the first match from `awk`. In this case, print the filenames `|`‘ed through `awk`.


```
jemurray@phalanges:~/Downloads $ for i in `IMG_17*.jpg`; do echo $i | awk '{print "[![Image of philmont prep hike](/images/" $1" )](/images/" $1 ")"}'; done
[![Image of philmont prep hike](/images/IMG_1706.jpg )](/images/IMG_1706.jpg)
[![Image of philmont prep hike](/images/IMG_1707.jpg )](/images/IMG_1707.jpg)
[![Image of philmont prep hike](/images/IMG_1708.jpg )](/images/IMG_1708.jpg)
[![Image of philmont prep hike](/images/IMG_1711.jpg )](/images/IMG_1711.jpg)
[![Image of philmont prep hike](/images/IMG_1712.jpg )](/images/IMG_1712.jpg)
[![Image of philmont prep hike](/images/IMG_1713.jpg )](/images/IMG_1713.jpg)
[![Image of philmont prep hike](/images/IMG_1714.jpg )](/images/IMG_1714.jpg)
[![Image of philmont prep hike](/images/IMG_1715.jpg )](/images/IMG_1715.jpg)
[![Image of philmont prep hike](/images/IMG_1716.jpg )](/images/IMG_1716.jpg)
[![Image of philmont prep hike](/images/IMG_1717.jpg )](/images/IMG_1717.jpg)
[![Image of philmont prep hike](/images/IMG_1718.jpg )](/images/IMG_1718.jpg)
[![Image of philmont prep hike](/images/IMG_1720.jpg )](/images/IMG_1720.jpg)
[![Image of philmont prep hike](/images/IMG_1722.jpg )](/images/IMG_1722.jpg)
```

The commands above generated this page: https://jasonmurray.org/posts/2021/firstlongphilmontprephike/
