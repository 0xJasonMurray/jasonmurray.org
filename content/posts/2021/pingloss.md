---
title: "Log ping loss over time"
date: 2021-01-13T11:08:25-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ping
  - linux
  - oneliner
---

A `one liner` to output rolling ping statistics to a specific host:

```
jemurray@home-server:~$ while [ 1 ]; do echo -n "`date` "; ping -c 5 -i .2 shell.jasonmurray.org | grep -P '^\d.*packets'; sleep 1; done
Wed Jan 13 11:07:25 CST 2021 5 packets transmitted, 5 received, 0% packet loss, time 803ms
Wed Jan 13 11:07:27 CST 2021 5 packets transmitted, 5 received, 0% packet loss, time 802ms
Wed Jan 13 11:07:29 CST 2021 5 packets transmitted, 5 received, 0% packet loss, time 801ms
```

Piecing together tools like this is core to the [Unix Philosopy](https://en.wikipedia.org/wiki/Unix_philosophy).  It is fun to see how we can extend simple commands to make something more complex.