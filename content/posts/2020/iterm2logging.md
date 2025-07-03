---
title: "Persistant terminal logging in iTerm2"
date: 2020-05-25T05:35:16-05:00
toc: false
images:
categories:
  - tech
tags: 
  - iterm2
  - logging
---

# Summary

Enable terminal logging for all sessions: 

```
iTerm2 -> Preferences... -> Profiles (Default) -> Session -> Automatically Log... -> /path/to/logfiles
```


# Details

Much of my day is spent in a terminal session using `ssh`, `vim`, or the Unix `shell`.

Terminal logging is invaluable to capture output, remember command syntax, review troubleshooting steps, or go back in history.  

[iTerm2](https://www.iterm2.com/) has a feature that automatically logs all terminal output to a text file.

Configuration:

![iterm configuration](/images/iterm2logging.png)

Over the past 4 years, I have collected a lot of log files:

```
jemurray@mbp-2019:~/g/iterm2-logging $ ls -1 | wc
    2723    2728  227389
```

Four years of logs:

```
jemurray@mbp-2019:~/g/iterm2-logging $ ls -alt | head -5
total 35597224
-rw-r--r--     1 jemurray  staff        1776 May 10 10:44 20200510_103627.Default.w4t0p0.060ACCBF-0953-4BEA-88EA-D2DC272FB3DA.405.528106028.log
-rw-r--r--     1 jemurray  staff    10075727 May 10 10:43 20200428_124338.Default.w0t2p0.0DB16AD6-B1FB-4A3E-BA85-93CE1D397CAF.384.3024836256.log
-rw-r--r--     1 jemurray  staff     4925964 May 10 10:36 20200428_124339.Default.w0t5p0.FF858E4B-CCB2-4213-8896-8355FA8F378A.384.4100644332.log

jemurray@mbp-2019:~/g/iterm2-logging $ ls -alt | tail -5
-rw-r--r--     1 jemurray  staff       46308 Jan 22  2016 20160122_135456.Default.w1t1p0.735.fbf2fb0a.log
-rw-r--r--     1 jemurray  staff     7343991 Jan 22  2016 20160120_120817.Default.w1t3p0.735.f822c1d1.log
-rw-r--r--     1 jemurray  staff        2567 Jan 14  2016 20160114_045325.Default.w8t0p0.735.34409783.log
-rw-r--r--     1 jemurray  staff       27558 Jan 13  2016 20160113_201937.Default.w8t0p0.735.da298097.log
-rw-r--r--     1 jemurray  staff      353641 Jan 13  2016 20160113_201804.Default.w8t0p0.735.ba8e77f6.log
```

Text files can take up a lot of space:

```
jemurray@mbp-2019:~/g $ du -hs iterm2-logging/
 17G    iterm2-logging/
```

I very rarely close old terminal sessions or reboot my laptop, this leads to some very large individual files:

```
jemurray@mbp-2019:~/g/iterm2-logging $ ls -alSh | head -10
total 35598120
-rw-r--r--     1 jemurray  staff   1.2G May  2  2016 20160429_093314.Default.w2t0p0.802.1946ca48.log
-rw-r--r--     1 jemurray  staff   1.1G Aug 28  2019 20190819_095649.Default.w0t4p0.FBAD01A0-E0ED-4D64-ACE8-A22F7FD4A9E5.1066.2495880441.log
-rw-r--r--     1 jemurray  staff   938M May 23  2019 20190410_163139.Default.w0t0p0.FEB70CD3-5F75-4487-8BD1-9158E8B0EB19.696.3815081510.log
-rw-r--r--     1 jemurray  staff   788M Jul  9  2019 20190619_092441.Default.w0t0p0.6FFF2FB0-92B7-4C0B-BB07-1E76179CEC45.15326.720948035.log
-rw-r--r--     1 jemurray  staff   437M Jan 30 15:33 20191220_060302.Default.w0t1p0.70425FB4-94E4-4EE4-902D-DCB0014342A1.459.720066674.log
-rw-r--r--     1 jemurray  staff   377M May 29  2019 20190523_181326.Default.w0t0p0.4C97D4F3-EAA1-4D9E-83ED-B69A9DDC597E.20131.238486017.log
-rw-r--r--     1 jemurray  staff   347M Jun  7  2019 20190529_100334.Default.w0t0p0.97FCF5BD-AB86-497E-B3EB-0420D35AEAE3.1007.401592612.log
-rw-r--r--     1 jemurray  staff   315M Apr 13 10:21 20200327_170826.Default.w0t7p1.FACE7567-2B9C-4CEE-B318-AC25F54E5C16.1220.2691320413.log
-rw-r--r--     1 jemurray  staff   299M Jan 23  2018 20180120_140049.Default.w0t0p0.C7F53546-43AC-4622-B42C-E4F8B097BC43.2227.665929603.log
```
