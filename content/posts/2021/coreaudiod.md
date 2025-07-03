---
title: "macOS coreaudiod high CPU utilization with Firefox or Chrome"
date: 2021-07-27T16:49:34-05:00
toc: false
images:
categories:
  - tech
tags: 
  - macos
  - coreaudiod
  - firefox
  - chrome
description: The macOS process coreaudiod uses a lot of cpu when running Office365 and/or Google online web based office suite software.
---

For weeks the fans in my MacbookPro 15" laptop have been running at full speed, the aluminum case is hot to the touch, and the battery hardly lasts for more then a few hours. 

While searching for the culprit, I regularly notice the process `coreaudiod` is using between 10 and 20 percent of the cpu.

```
_coreaudiod      96616   17.4  0.1  6098756  30144   ??  Ss   13Jul21 1679:26.11 /usr/sbin/coreaudiod
```

Using `lsof`, we can narrow down what processes are utilizing the `coreaudiod` services:

```
jemurray@phalanges:~ $ lsof | grep -i coreaudio
corespeec   476 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
com.apple   552 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
PowerChim   773 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co  3637 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co  3639 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co  3716 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co  3901 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Microsoft 19221 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
iTerm2    19362 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Messages  19397 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Google    22063 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Slack\x20 39234 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co 40796 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
QuickLook 46894 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Microsoft 48972 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Microsoft 48975 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Microsoft 82923 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co 82995 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
plugin-co 82996 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
Music     92477 jemurray  txt       REG                1,4     3306960 1152921500312120220 /System/Library/Components/CoreAudio.component/Contents/MacOS/CoreAudio
```

In this example, the `plugin-container` process is the sandbox environment `firefox` uses to keep tabs isolated. I started the search here since there were so many containers running.

To narrow down the problem, I started by examining the individual tabs within `firefox` `task manager` and noticed that `outlook email`, `outlook calendar`, `gmail`, and `google calendar` were all using a significant amount of energy.  For example:

[![Image of ](/images/2021-07-27-17-02-40.png)](/images/2021-07-27-17-02-40.png)

To test my theory, I closed all four of these tabs and the `coreaudiod` CPU utilization went to zero:

```
_coreaudiod      96616   0.0  0.1  6098232  23836   ??  Ss   13Jul21 1679:26.69 /usr/sbin/coreaudiod
```

However, I use these applications throughout the day. They must be running. As a test, I opened all four tabs in `safari` to see if Apple did a better job with CPU utilization.  Spoiler, it does.  The `coreaudiod` process is still zero and the fans no longer run at full speed.

Why not switch to `safari` exclusively? Firefox and Chrome have better support for ad blocking and some other containerization plugins I use for privacy.