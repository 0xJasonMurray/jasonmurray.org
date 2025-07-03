---
title: "Find the typo with diff"
date: 2020-09-16T13:23:05-05:00
toc: false
images:
categories:
  - tech
tags: 
  - syslog
  - diff
  - troubleshooting
---

Use `diff`, find the error, fix the error, make everyone happy.  Syslog works again.

In this example, diff is used to compare the original file that works, with the new configuration file that breaks syslog.  Can you spot the typo?

Scan for the changes in the side-by-side comparison.  Pay close attention to the differences in patterns.  I bet you didn't realize [Sesame Street](https://www.youtube.com/watch?v=rsRjQDrDnY8) has been preparing us this entire time to spot errors in syslog configuration files:

```
[jemurray@syslogserver rsyslog.d]$ diff -y -W 120 34-mylog.conf 34-mylog.conf.change.for.splunk.rolled.back-JEM
# My Servers                                                    # My Servers
:FROMHOST, isequal, "192.168.35.1" /var/log/mylog.log     |    FROMHOST, isequal, "192.168.35.1" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.35.2" /var/log/mylog.log     |    :FROMHOST, isequal, "192.168.35.2" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.35.129" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.35.129" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.35.130" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.35.130" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.36.65" /var/log/mylog.log    |    :FROMHOST, isequal, "192.168.36.65" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.36.66" /var/log/mylog.log    |    :FROMHOST, isequal, "192.168.36.66" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.36.193" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.36.193" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.36.194" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.36.194" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.112.30" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.112.30" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.112.31" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.112.31" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.112.32" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.112.32" /var/log/mynewlog.log
& ~                                & ~
:FROMHOST, isequal, "192.168.112.33" /var/log/mylog.log   |    :FROMHOST, isequal, "192.168.112.33" /var/log/mynewlog.log
```

Did you find the missing: `:`?  Hint, look at the second line.

